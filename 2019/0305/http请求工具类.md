### http请求。普通http请求，以及绕过https请求。当时绕过https需要jar包httpclient和httpcore版本4.5及以上

~~~
public class HttpClientUtil {

    public static CloseableHttpClient createSSLClientDefault() {
        try {
            SSLContext sslContext = new SSLContextBuilder().loadTrustMaterial(null, new TrustStrategy() {
                // 信任所有
                public boolean isTrusted(X509Certificate[] chain, String authType) throws CertificateException {
                    return true;
                }
            }).build();
            HostnameVerifier hostnameVerifier = NoopHostnameVerifier.INSTANCE;
            SSLConnectionSocketFactory sslsf = new SSLConnectionSocketFactory(sslContext, hostnameVerifier);
            return HttpClients.custom().setSSLSocketFactory(sslsf).build();
        } catch (KeyManagementException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (KeyStoreException e) {
            e.printStackTrace();
        }
        return HttpClients.createDefault();

    }
        //普通的http请求
        public static CloseableHttpResponse excute(Map<String,String> stringMap) throws URISyntaxException, IOException {

            //创建一个httpclient对象
            CloseableHttpClient httpClient = HttpClients.createDefault();
            //创建一个uri对象
            URIBuilder uriBuilder = new URIBuilder(stringMap.get("uri"));
            for (Map.Entry<String, String> entry : stringMap.entrySet()) {

                String key = entry.getKey();
                String value = entry.getValue();
                if(!"uri".equals(key)){
                    uriBuilder.addParameter(key,value);
                }
            }
            HttpGet get = new HttpGet(uriBuilder.build());
            //执行请求
            CloseableHttpResponse response = httpClient.execute(get);
            //取响应的结果
            int statusCode = response.getStatusLine().getStatusCode();
            return response;
        }

    //绕过https
    public static CloseableHttpResponse excuteHTTPSPost(String uri,Map<String,Object> json) throws URISyntaxException, IOException {

        //创建一个httpclient对象
        CloseableHttpClient httpClient = createSSLClientDefault();
        //创建一个uri对象
        UrlEncodedFormEntity uefEntity = null;
        CloseableHttpResponse response = null;

        HttpPost httpPost = new HttpPost(uri);
        StringEntity stringEntity = new StringEntity(JSON.toJSONString(json),"UTF-8");//解决中文乱码问题
        stringEntity.setContentEncoding("UTF-8");
        stringEntity.setContentType("application/json");
        httpPost.setEntity(stringEntity);
        //填充实体
        //执行请求
        response = httpClient.execute(httpPost);

        return  response;
    }
    //绕过https
    public static CloseableHttpResponse excuteHTTPSGET(Map<String,String> stringMap) throws URISyntaxException, IOException {

        //创建一个httpclient对象
        CloseableHttpClient httpClient = createSSLClientDefault();
        //创建一个uri对象
        URIBuilder uriBuilder = new URIBuilder(stringMap.get("uri"));
        for (Map.Entry<String, String> entry : stringMap.entrySet()) {

            String key = entry.getKey();
            String value = entry.getValue();
            if(!"uri".equals(key)){
                uriBuilder.addParameter(key,value);
            }

        }
        HttpGet get = new HttpGet(uriBuilder.build());
        //执行请求
        CloseableHttpResponse response = httpClient.execute(get);
        //取响应的结果
        int statusCode = response.getStatusLine().getStatusCode();
        return response;
    }
    //http请求结果转json
    public static JSONObject getJsonString(CloseableHttpResponse excute) throws IOException, URISyntaxException {
        HttpEntity entity = excute.getEntity();
        InputStream is = entity.getContent();
        //读取输入流，即返回文本内容
        StringBuffer sb = new StringBuffer();
        BufferedReader br = new BufferedReader(new InputStreamReader(is, "utf-8"));
        String line = "";
        while ((line = br.readLine()) != null) {
            sb.append(line);
        }
        JSONObject jsonObject = JSONObject.parseObject(sb.toString());
        return jsonObject;
    }
}

~~~