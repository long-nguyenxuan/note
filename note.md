# note-ubuntu 
## run nhiều dòng lệnh một lần
### dùng dấu ; 
cái này thì đơn thuần là chạy từng dòng lệnh 1 

```bash 
ls ; pwd ; whoami
```

### dùng dấu  && 
chỉ thực hiện lệnh sau khi lệnh trước thành công 
```bash 
mkdir MyFolder && cd MyFolder
```

### dùng dấu ||
thực hiện lệnh sau khi lệnh trc không thành công 

### dùng kết hợp 
check một package XXX đã tồn tại

```bash 
apt list xxx 
```

## getting started 
### bộ gõ tiếng việt
```
$ sudo apt-get install ibus-unikey
```

trường hợp méo có sẵn ppa của ibus 
```
$ sudo add-apt-repository ppa:ubuntu-vn/ppa
$ sudo apt-get update
```

khởi động lại ibus 
```
$ ibus restart
```
thiết lập bộ gõ: 
```
https://vinasupport.com/huong-dan-cai-bo-go-tieng-viet-ibus-unikey-tren-ubuntu/
```
### app list
vscode
ranger
