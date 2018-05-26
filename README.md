# 修改fis3的/lib/compile.js v1.2.1

功能说明及使用：
--------------

1. 更新fis3到最新版（目前实测版本为v3.4.39）

    ```sh       
    npm update -g fis3
    ```

2. 通过 fis3 -v 查看fis3的安装目录。找到 /fis3安装目录/lib/compile.js 进行编辑


3. 对 qll-img 和 qll-bg 进行资源定位

    * 修改 Line 423：
    ```
        source: var reg = /(<script(?:(?=\s)[\s\S]*?["'\s\w\/\-]>|>))([\s\S]*?)(?=<\/script\s*>|$)|........... 
        在 "var reg = /" 后增加 ((qll-img|qll-bg)=\s?"[\s\S]*?")|
    ```

    * 修改 Line 424：
    ```
        source: callback = callback || function(m, ...........
        在 "callback = callback || function(m, " 后增加： $qll_img, $qll_bg, 
    ```

    * Line 504 前 插入：
    ```javascript
        m = m.replace(/(qll-img=\s*)('[^']+'|"[^"]+"|[^\s\/>]+)/ig, function(_, prefix, value) {
            // console.log(prefix, value);
            return prefix + map.uri.wrap(value);
        });
        m = m.replace(/(qll-bg=\s?".*url\()('[^']+'|"[^"]+"|[^\s\/>]+)(.*")/ig, function(_, prefix, value, overfix) {
            // console.log(prefix, value, overfix);
            return prefix + map.uri.wrap(value) + overfix;
        });
    ```


4. 对data-main进行资源定位

    * Line 435 前 插入：
    ```javascript
        $1 = $1.replace(/(\sdata-main\s*=\s*)('[^']+'|"[^"]+"|[^\s\/>]+)/ig, function(m, prefix, value) {
            var reg = new RegExp(/['"]\/widget\/(?:(.+)\/)?main\.js['"]/ig);
            var result = reg.exec(value);

            if (result) {
              var dir = result[1] ? "_" + result[1].replace("/", "_") : "";
              value = "\"/widget/aio" + dir + ".js\"";
            }
            if (isInline(fis.util.query(value))) {
              embed += map.embed.wrap(value);
              return '';
            } else {
              return prefix + map.uri.wrap(value);
            }
        });
    ```
    > 支持多个aio.js
    > > 比如widget/app下有mp和my两个文件夹，里面分别有一个main入口。
    > 
    > > 在视图中，data-main分别引用/widget/app/mp/main.js和/widget/app/my/main.js
    > 
    > > r.js中，需要把mp和my中的js分别打包为/widget/aio_app_mp.js和/widget/aio_app_my.js

更新记录：
--------------

v1.2.2

        1. 修改data-main的bug

v1.2.1

        1. 增加了多个aio.js的支持

v1.1.2

        1. 增加了Line 424行的修改，详见使用说明.4
        1. 修改compile文件名，注明是基于3.4.33的示例，不同版本的fis不建议直接覆盖文件。

v1.1.1

        1. v3.4.33 对data-main、qll-img、qll-bg 进行资源定位
        