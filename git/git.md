# git配置

## 最小配置

配置user.name和user.email

```bash
git config --global user.name 'your_name'
git config --global user.email 'your_email@domain.com'
```

git config 作用域

```bash
git config --local   # 只对某个某个仓库有效，默认为此
git config --global  # 只对当前用户所有仓库有效
git config --system  # 对系统所有登录的用户有效
```

显示config的配置，加--list

```bash
git config --list --local
git config --list --global
git config --list --system
```



