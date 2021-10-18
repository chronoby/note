## git

- clone 
```
git config --global --unset http.proxy
```

- push

git config --global http.sslverify false

### 镜像

子模块

修改.gitmodules，把子项目中的url地址同样加上.cnpmjs.org后缀，然后利用`git submodule sync`更新子项目对应的url，最后再`git submodule update --init --recursive`
