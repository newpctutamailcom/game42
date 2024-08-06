# game42
    <script src="https://cdn.jsdelivr.net/npm/jszip/dist/jszip.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/file-saver@2.0.5/dist/FileSaver.min.js"></script>

    <script>
        (function() {
            var originalFetch = window.fetch;

            window.fetch = function(url, options) {
                return originalFetch(url, options)
                    .then(response => {
                        if (!response.ok) {
                            console.error('Fetch Error:', response.status, url);
                            // 处理404错误，重定向到另一个域名
                            if (response.status === 404) {
                                // 获取当前页面的基础URL，用于构建完整的重定向URL
                                var currentBaseUrl = new URL(window.location.href);

                                // 解析原始URL
                                var originalUrl = new URL(url, currentBaseUrl);

                                // 构建新的重定向URL，例如将请求重定向到另一个域名
                                var newBaseUrl = 'https://static.pgf-asqb7a.com';
                                var newUrl = new URL((originalUrl.pathname.startsWith("/shared")?"":"/42")+originalUrl.pathname + originalUrl.search, newBaseUrl);

                                // 使用新的URL重新发起fetch请求
                                return originalFetch(newUrl.toString(), options)
                                    .then(newResponse => {
                                        // 将newResponse转换为Blob对象
                                        return newResponse.blob()
                                            .then(blob => {
                                                // 使用JSZip库创建ZIP文件
                                                var zip = new JSZip();
                                                // 将Blob对象添加到ZIP文件中，文件名为原始URL的路径部分
                                                zip.file(originalUrl.pathname.startsWith("/")?originalUrl.pathname.substring(1):originalUrl.pathname, blob);
                                                // 生成ZIP文件并将其保存到本地
                                                zip.generateAsync({ type: "blob" })
                                                    .then(function(content) {
                                                        // 使用第三方库FileSaver.js保存ZIP文件到本地
                                                        saveAs(content, "download.zip");
                                                    });

                                                // 返回newResponse以继续传递
                                                return newResponse;
                                            });
                                    });
                            }
                        }
                        return response;
                    })
                    .catch(error => {
                        console.error('Fetch Error:', error);
                        throw error;
                    });
            };
        })();
    </script>
