---
title: jwt points
date: 2024-06-27 10:58:13
tags: learn
categories: jwt
---

JWT 由三个部分组成，用 . 分隔：

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJrZXkiOiJ1c2VyLWtleSIsImV4cCI6MTU2NDA1MDgxMX0.Us8zh_4VjJXF-TmR5f8cif8mBU7SuefPlpxhH0jbPVI
```



​	1.	Header（头部）：eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9

​	2.	Payload（负载）：eyJrZXkiOiJ1c2VyLWtleSIsImV4cCI6MTU2NDA1MDgxMX0

​	3.	Signature（签名）：Us8zh_4VjJXF-TmR5f8cif8mBU7SuefPlpxhH0jbPVI



- **可直接使用 Base64 解码 Header 和 Payload**

- **signature验签**

  使用 Header里面的算法验证签名需要用到与签名时相同的密钥（公钥）。以下是验证签名的示例代码：

  ```python
  import hmac
  import hashlib
  
  def verify_signature(header, payload, secret, signature):
      message = f"{header}.{payload}"
      expected_signature = hmac.new(secret.encode(), message.encode(), hashlib.sha256).digest()
      expected_signature_base64 = base64.urlsafe_b64encode(expected_signature).rstrip(b'=').decode('utf-8')
      return hmac.compare_digest(expected_signature_base64, signature)
  
  # 示例密钥（替换为实际的密钥）
  secret = "your-256-bit-secret"
  
  # 验证签名
  is_valid = verify_signature(header, payload, secret, signature)
  print(f"Signature is valid: {is_valid}")
  ```

  