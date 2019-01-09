```
    /**
	 * 微信退款
	 * 
	 * @param orderNo
	 * @param amount
	 */
	public static Ret wechatRefund(String orderNo, BigDecimal amount) {
		Prop prop = PropKit.use("wechat.txt");
		// 封装请求参数
		Map<String, String> data = new HashMap<>();
		data.put("appid", prop.get("appid"));
		data.put("mch_id", prop.get("mchid"));
		data.put("nonce_str", StrKit.getRandomUUID());
		data.put("out_trade_no", orderNo);
		data.put("out_refund_no", orderNo);
		String totalFee = String.valueOf(amount.multiply(new BigDecimal("100")).intValue());
		data.put("total_fee", totalFee);
		data.put("refund_fee", totalFee);
		data.put("sign", StringKit.genMd5Sign(data, prop.get("key")));
		// 发送退款请求
		String result = null;
		try {
			// 实例化密码库并设置证书格式
			KeyStore keyStore = KeyStore.getInstance("PKCS12");
			// 将证书文件转为文件输入流
			FileInputStream inputStream = new FileInputStream(new File(prop.get("cert_path")));
			// 加载证书文件流和密码（默认为商户id）到密钥库
			keyStore.load(inputStream, prop.get("mchid").toCharArray());
			SSLContext sslcontext = SSLContexts.custom().loadKeyMaterial(keyStore, prop.get("mchid").toCharArray())
					.build();
			SSLConnectionSocketFactory sslsf = new SSLConnectionSocketFactory(sslcontext);
			// 构建ssl套接字的证书内容和密码
			CloseableHttpClient httpClient = HttpClients.custom().setSSLSocketFactory(sslsf).build();
			// 创建post请求
			HttpPost httpPost = new HttpPost(prop.get("refund_url"));
			// 填充数据实体
			httpPost.setEntity(new StringEntity(StringKit.mapToXml(data), Const.CHARSET));
			// 发送退款请求
			HttpResponse response = httpClient.execute(httpPost);
			HttpEntity entity = response.getEntity();
			result = EntityUtils.toString(entity, Const.CHARSET);
		} catch (Exception e) {
			e.printStackTrace();
			return Ret.fail("msg", ErrorMsg.REQUEST_FAIL);
		}
		if (StrKit.isBlank(result))
			return Ret.fail("msg", ErrorMsg.REQUEST_FAIL);
		Map<String, String> response = StringKit.xmlToMap(result);
		// 请求成功需返回状态和返回结果均为成功
		if (response == null || !response.get("return_code").equals(UserPaymentRecord.WechatResult.CODE)
				|| !response.get("result_code").equals(UserPaymentRecord.WechatResult.CODE)) {
			LogKit.error("请求微信退款失败：" + response.get("return_msg"));
			return Ret.fail("msg", ErrorMsg.REQUEST_FAIL);
		}
		return Ret.ok();
	}
 
	/**
	 * 支付宝退款
	 * 
	 * @param orderNo
	 * @param amount
	 */
	public static Ret alipayRefund(String orderNo, BigDecimal amount) {
		Prop prop = PropKit.use("alipay.txt");
		// 获取请求参数
		String serverUrl = prop.get("open_api_domain");
		String appid = prop.get("appid");
		String privateKey = prop.get("private_key");
		String format = "JSON";
		String charset = "UTF-8";
		String alipayPublicKey = prop.get("alipay_public_key");
		String signType = prop.get("sign_type");
		// 实例化支付宝客户端
		AlipayClient alipayClient = new DefaultAlipayClient(serverUrl, appid, privateKey, format, charset,
				alipayPublicKey, signType);
		AlipayTradeRefundRequest request = new AlipayTradeRefundRequest();
		// 填充业务内容
		Map<String, String> bizContent = new HashMap<>();
		bizContent.put("out_trade_no", orderNo);
		bizContent.put("refund_amount", amount.toString());
		request.setBizContent(JsonKit.toJson(bizContent));
		try {
			// 发送退款请求
			if (alipayClient.execute(request).isSuccess())
				return Ret.ok();
		} catch (AlipayApiException e) {
			e.printStackTrace();
			return Ret.fail("msg", ErrorMsg.REQUEST_FAIL);
		}
		return Ret.fail("msg", ErrorMsg.OPERATION_FAIL);
	}
```



