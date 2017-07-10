# 修改fis3的/lib/compile.js v1.1.1

功能说明及使用：
--------------

1. 更新fis3到最新版（目前实测版本为v3.4.33）
        
        npm update -g fis3

2. 通过 fis3 -v 查看fis3的安装目录。找到 /fis3安装目录/lib/compile.js 进行编辑


3. 对data-main进行资源定位

        * Line 435 前 插入：
        $1 = $1.replace(/(\s(?:data-)?main\s*=\s*)('[^']+'|"[^"]+"|[^\s\/>]+)/ig, function(m, prefix, value) {
            if (value == "\"/widget/main.js\"")
                value = "\"/widget/aio.js\""
            if (isInline(fis.util.query(value))) {
                embed += map.embed.wrap(value);
                return '';
            } else {
                return prefix + map.uri.wrap(value);
            }
        });

4. 对 qll-img 和 qll-bg 进行资源定位

        * 修改 Line 423：
        source: var reg = /(<script(?:(?=\s)[\s\S]*?["'\s\w\/\-]>|>))([\s\S]*?)(?=<\/script\s*>|$)|........... 
        在 "var reg = /" 后增加 ((qll-img|qll-bg)=\s?"[\s\S]*?")|

        * Line504 前 插入：
        m = m.replace(/(qll-img=\s*)('[^']+'|"[^"]+"|[^\s\/>]+)/ig, function(_, prefix, value) {
            // console.log(prefix, value);
            return prefix + map.uri.wrap(value);
        });
        m = m.replace(/(qll-bg=\s?".*url\()('[^']+'|"[^"]+"|[^\s\/>]+)(.*")/ig, function(_, prefix, value, overfix) {
            // console.log(prefix, value, overfix);
            return prefix + map.uri.wrap(value) + overfix;
        });

更新记录：
--------------

v1.1.1

        1. v3.4.33 对data-main、qll-img、qll-bg 进行资源定位
        