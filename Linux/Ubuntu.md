### 变更Python版本，将Python3设置为默认值
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 150

## 开启22端口的SSH

### 安装SSH

sudo apt install ssh

/etc/init.d/ssh start

### 开户22端口防火墙

sudo ufw allow 22/tcp

### 查看防火墙状况

sudo ufw status

## 设置root账号密码

sudo passwd root