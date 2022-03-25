### GitHub Actions 自动化部署

### GitHub Access Token
- 打开GitHub Settings 选择 Settings Developer settings，找到Personal access tokens

- 点击Generate new token，Note输入名字TOKEN,Select scopes选择token拥有的权限，勾选repo,拥有完全访问的权限。

- 点击创建，复制创建成功的令牌，后续需要用到，如果忘记重新创建。

- 打开代码的远程仓库，点击Settings中的Secrets,选择Actions secrets。点击New repository secret，Name为TOKEN,和之前的保持一致，Value则是之前复制创建成功的令牌。

- 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

### 配置GitHub Actions 执行脚本

- 在项目根目录创建.github/workflows目录

- 下载main.yml到workflows目录中

- 修改配置文件

- 配置PM2配置文件

- 提交更新

- 查看自动部署状态

- 访问网站

- 提交更新


### main.yml

```
name: Publish And Deploy Demo
on:
  push:
    tags:
      - 'v*'

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:

    # 下载源码
    - name: Checkout
      uses: actions/checkout@master

    # 打包构建
    - name: Build
      uses: actions/setup-node@master
    - run: npm install
    - run: npm run build
    - run: tar -zcvf release.tgz .nuxt static nuxt.config.js package.json package-lock.json pm2.config.json

    # 发布 Release
    - name: Create Release
      id: create_release
      uses: actions/create-release@master
      env:
        GITHUB_TOKEN: ${{ secrets.TOKEN }}
      with:
        tag_name: ${{ github.ref }}
        release_name: Release ${{ github.ref }}
        draft: false
        prerelease: false

    # 上传构建结果到 Release
    - name: Upload Release Asset
      id: upload-release-asset
      uses: actions/upload-release-asset@master
      env:
        GITHUB_TOKEN: ${{ secrets.TOKEN }}
      with:
        upload_url: ${{ steps.create_release.outputs.upload_url }}
        asset_path: ./release.tgz
        asset_name: release.tgz
        asset_content_type: application/x-tgz

    # 部署到服务器
    - name: Deploy
      uses: appleboy/ssh-action@master
      with:
        host: ${{ secrets.HOST }}
        username: ${{ secrets.USERNAME }}
        password: ${{ secrets.PASSWORD }}
        port: ${{ secrets.PORT }}
        script: |
          cd /root/realworld-nuxtjs
          wget https://github.com/itsRaul/realworld-nuxtjs/releases/latest/download/release.tgz -O release.tgz
          tar zxvf release.tgz
          npm install --production
          pm2 reload pm2.config.json
```

- secrets.USERNAME：打开代码的远程仓库，点击Settings中的Secrets,选择Actions secrets，name为USERNAME，value为服务器的名字,如root
- secrets.PORT：打开代码的远程仓库，点击Settings中的Secrets,选择Actions secrets，name为PORT，value为服务器的端口号,如默认的22
- secrets.HOST：打开代码的远程仓库，点击Settings中的Secrets,选择Actions secrets，name为HOST，value为服务器的ip地址
- secrets.PASSWORD：打开代码的远程仓库，点击Settings中的Secrets,选择Actions secrets，name为PASSWORD，value为服务器密码

### 自动部署

```
git add .
// 因为脚本main.yml的tag是以'v*'开头
git tag v0.1.0
git tag
git push origin v0.1.0
```

- 打开代码的远程仓库Actions,可以看到触发发布，点击标签，打开build-and-deploy可以看到自动部署的进度

### 部署更新

```
git add .
git commit -m "部署更新"
git tag
git tag v0.1.1
git push origin v0.1.1
```