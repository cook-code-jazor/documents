## WebSocket：响应头'Sec-WebSocket-Accept'的生成

```csharp
  private static string SHA1(byte[] clearData)
  {

      using (System.Security.Cryptography.SHA1 sha1 = System.Security.Cryptography.SHA1.Create())
      {
          return Convert.ToBase64String(sha1.ComputeHash(clearData));
      }
  }
```

```csharp
  //固定值，拼接到末尾
  byte[] salt = Encoding.ASCII.GetBytes("258EAFA5-E914-47DA-95CA-C5AB0DC85B11");

  //从客户端拿到Key
  string requestKey = request.GetHeader("Sec-WebSocket-Key");
  byte[] clearKey = Encoding.ASCII.GetBytes(requestKey);

  //生成响应Key，发送给客户端
  string responseKey = SHA1(clearKey.Concat(salt).ToArray());
  response.SetHeader("Sec-WebSocket-Accept", responseKey);
```

逻辑：Base64Encode(SHA1BytesResult([Sec-WebSocket-Key] + '258EAFA5-E914-47DA-95CA-C5AB0DC85B11'))

SHA1BytesResult的返回值为20字节的SHA1结果，非40字节的HEX字符，所以Base64编码结果一定是28位。