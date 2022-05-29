# **Deploy project using Ansible**
## **Mục lục**
[**I. Yêu cầu**](https://github.com/nhok8t1/Viettel-Digital-Talent-Program-2022/edit/main/Practice-2/DoHoangSon/README.md#i-y%C3%AAu-c%E1%BA%A7u)

[**II. Ansible**](https://github.com/nhok8t1/Viettel-Digital-Talent-Program-2022/edit/main/Practice-2/DoHoangSon/README.md#ii-ansible)

   - [**1. Cài đặt Ansible**](https://github.com/nhok8t1/Viettel-Digital-Talent-Program-2022/edit/main/Practice-2/DoHoangSon/README.md#1-c%C3%A0i-%C4%91%E1%BA%B7t-ansible)
   - [**2. Khởi tạo inventory**](https://github.com/nhok8t1/Viettel-Digital-Talent-Program-2022/edit/main/Practice-2/DoHoangSon/README.md#2-kh%E1%BB%9Fi-t%E1%BA%A1o-inventory)
   - [**3. Clone git repo**](https://github.com/nhok8t1/Viettel-Digital-Talent-Program-2022/edit/main/Practice-2/DoHoangSon/README.md#3-clone-git-repo)
   - [**4. Đóng gói WAR file**](https://github.com/nhok8t1/Viettel-Digital-Talent-Program-2022/edit/main/Practice-2/DoHoangSon/README.md#4-%C4%91%C3%B3ng-g%C3%B3i-war-file)
   - [**5. Cài đặt Tomcat server và deploy project**](https://github.com/nhok8t1/Viettel-Digital-Talent-Program-2022/edit/main/Practice-2/DoHoangSon/README.md#5-c%C3%A0i-%C4%91%E1%BA%B7t-tomcat-server-v%C3%A0-deploy-project)

[**III. Các bước thực hiện**](https://github.com/nhok8t1/Viettel-Digital-Talent-Program-2022/edit/main/Practice-2/DoHoangSon/README.md#iii-c%C3%A1c-b%C6%B0%E1%BB%9Bc-th%E1%BB%B1c-hi%E1%BB%87n)

[**IV. Kết quả**](https://github.com/nhok8t1/Viettel-Digital-Talent-Program-2022/edit/main/Practice-2/DoHoangSon/README.md#iv-k%E1%BA%BFt-qu%E1%BA%A3)
[**V. Tài liệu tham khảo**](https://github.com/nhok8t1/Viettel-Digital-Talent-Program-2022/edit/main/Practice-2/DoHoangSon/README.md#v-t%C3%A0i-li%E1%BB%87u-tham-kh%E1%BA%A3o)
## **I. Yêu cầu**
**Hệ điều hành**: Ubuntu Server (Ubuntu Server 22.04).
**Phần mềm ảo hóa**: Oracle Virtualbox (Oracle Virtualbox 6.1.34)
**Cấu hình máy ảo:**
- 2 NICs:
    + NAT (enp0s3): 10.0.2.15/24
    + Host-only Adapter (enp0s8): 192.168.56.108/24

## **II. Ansible**
Ansible là một trong những công cụ quản lý cấu hình hiện đại, nó tạo điều kiện thuận lợi cho công việc cài đặt, quản lý và bảo trì các server từ xa, với thiết kế tối giản giúp người dùng cài đặt và chạy nhanh chóng.Người dùng viết các tập lệnh cấp phép Ansible trong YAML, một tiêu chuẩn tuần tự hóa dữ liệu thân thiện với người dùng, chúng không bị ràng buộc với bất kỳ ngôn ngữ lập trình nào. Chính vì vậy người dùng có thể tạo ra các tập lệnh cấp phép phức tạp một cách trực quan hơn so với các công cụ còn lại trong cùng danh mục.
Thêm ảnh
Một số thuật ngữ cơ bản khi sử dụng Ansible:
- Controller Machine: Là máy cài Ansible, nó sẽ chịu trách nhiệm quản lý, điều khiển và gửi các task đến những máy con cần quản lý.
- Inventory: Là file chứa thông tin những server cần quản lý. File này thường nằm tại đường dẫn /etc/ansible/hosts.
- Playbook: Là file chứa các task được ghi dưới định dạng YAML. Máy controller sẽ đọc các task này trong Playbook sau đó đẩy các lệnh thực thi tương ứng bằng Python xuống các máy con.
- Task: Một block ghi lại những tác vụ cần thực hiện trong playbook và các thông số liên quan.
- Module: Trong Ansible có rất nhiều module khác nhau. Ansible hiện có hơn 2000 module để thực hiện các tác vụ khác nhau, bạn cũng có thể tự viết thêm những module của mình khi có nhu cầu. Một số Module thường dùng cho những thao tác đơn giản như: System, Commands, Files, Database, Cloud, Windows,...
- Role: Là một tập playbook đã được định nghĩa để thực thi 1 tác vụ nhất định. Nếu bạn có nhiều server, mỗi server thực hiện những tasks riêng biệt. Và khi này nếu chúng ta viết tất cả vào cùng một file playbook thì khá là khó để quản lý. Do vậy roles sẽ giúp bạn phân chia khu vực với nhiệm vụ riêng biệt.
- Play: là quá trình thực thi một playbook.
- Facts: Thông tin của những máy được Ansible điều khiển, cụ thể sẽ là các thông tin về OS, system, network,…
- Handlers: Được sử dụng để kích hoạt những thay đổi của dịch vụ như start, stop service.
- Variables: Được dùng để lưu trữ các giá trị và có thể thay đổi được giá trị đó. Để khai báo biến, người dùng chỉ cần sử dụng thuộc tính vars đã được Ansible cung cấp sẵn.
- Conditions: Ansible cho phép người dùng điều hướng lệnh chạy hay giới hạn phạm vi để thực hiện câu lệnh nào đó. Hay nói cách khác, khi thỏa mãn điều kiện thì câu lệnh mới được thực thi. Ngoài ra, Ansible còn cung cấp thuộc tính Register, một thuộc tính giúp nhận câu trả lời từ một câu lệnh. Sau đó ta có thể sử dụng chính kết quả đó để chạy những câu lệnh sau.
## **III. Các bước thực hiện**
### **1. Cài đặt Ansible**
- Cập nhật các gói
```
sudo apt update && sudo apt upgrade
```
- Cài đặt Ansible. 
```
sudo apt-get install -y  software-properties-common
sudo apt-add-repository --yes --update ppa:ansible/ansible
sudo apt-get update
sudo apt-get install -y ansible
```
- Cấu hình Ansible config file `/etc/ansible/ansible.cfg`
```
[defaults]
host_key_checking=False
```
### **2. Khởi tạo inventory**
- Bạn có thể dùng inventory mặc định tại đường dẫn /etc/ansible/hosts hoặc tự tạo custom inventory. Ở đây mình đặt tên group là servers với các host và biến được định nghĩa như sau:
```
[servers]
localhost ansible_connection=local
192.168.56.108 ansible_ssh_user=dohoangson ansible_ssh_pass=Namdinh123@
```
### **3. Clone git repo**
- Khởi tạo git.yml
```
---
 - hosts: all
   tasks:
   - name: Clone a github repository
     git:
       repo: https://github.com/nhok8t1/FrontEnd.git
       dest: /home/dohoangson/git_environment/
       clone: yes
       update: yes
```
- Chạy playbook git.yml
```
ansible-playbook -i inventory git.yml
```
- Kết quả chạy
Chèn ảnh
### **4. Đóng gói WAR file**
- Khởi tạo war.yml
```
---
- name: Build WAR file from project
  hosts: servers

  tasks: 
    - name: Create WAR file
      shell: jar -cvf FrontEnd.war *
      args:
        chdir: /home/dohoangson/git_environment
```
- Chạy playbook war.yml
```
ansible-playbook -i inventory git.yml
```
- Kết quả chạy
> Thêm ảnh

### **5. Cài đặt Tomcat server và deploy project**
- Playbook này thực hiện thêm nhóm, thêm người dùng, download tomcat server về, set quyền thư mục tomcat, copy file war đã tạo ở trên để deploy vào server. Dưới đây là file tomcat.yml
```
---
- name: Install Apache Tomcat10 using ansible
  hosts: servers
  become: true
  tasks:
    - name: add group "tomcat"
      group: name=tomcat

    - name: add user "tomcat"
      user: name=tomcat group=tomcat home=/usr/share/tomcat createhome=no
      become: True
      become_method: sudo

    - name: Create a Tomcat Directory
      file:
        path: /opt/tomcat10
        owner: tomcat
        group: tomcat
        mode: 755
        recurse: yes

    - name: download & unarchive tomcat10
      unarchive:
        src: https://archive.apache.org/dist/tomcat/tomcat-10/v10.0.21/bin/apache-tomcat-10.0.21.tar.gz
        dest: /opt/tomcat10
        remote_src: yes
        extra_opts: [--strip-components=1]

    - name: Change ownership of tomcat directory
      file:
        path: /opt/tomcat10
        owner: tomcat
        group: tomcat
        mode: "u+rwx,g+rx,o=rx"
        recurse: yes
        state: directory

    - name: Copy file
      copy:
        src: /home/dohoangson/git_environment/FrontEnd.war
        dest: /opt/tomcat10/webapps/FrontEnd.war
        mode: 777

    - name: Start and Enable Tomcat 10 on server
      shell: /opt/tomcat10/bin/startup.sh
      become: true
```
- Thực hiện chạy playbook này. Lưu ý cần thêm thuộc tính -K để yêu cầu hỏi password với những task cần quyền root
```
ansible-playbook -i inventory tomcat.yml -K
```
- Kết quả chạy
Thêm ảnh
## **IV. Kết quả**
Sau khi deploy thành công, project sẽ chạy trên cổng 8080 mặc định của tomcat với địa chỉ IP là địa chỉ máy chủ vừa khởi tạo
Thêm ảnh
## **V. Tài liệu tham khảo**
- [How to Clone a Git Repository with Ansible](https://linuxhandbook.com/clone-git-ansible/#:~:text=Cloning%20a%20Git%20Repository%20with%20Ansible%20playbook,-Now%20that%20you&text=Edit%20the%20file%20and%20add%20the%20following%20entries.&text=In%20the%20playbook%20above%2C%20you,to%20the%20SQLite%20GitHub%20repository.)
- [how to create war file from command line using ansible](https://stackoverflow.com/questions/57922048/how-to-create-war-file-from-command-line-using-ansible)
- [How to Install Apache tomcat using Ansible](https://automateinfra.com/2022/01/08/how-to-deploy-apache-tomcat-using-ansible/)