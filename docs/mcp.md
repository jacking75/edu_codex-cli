# MCP  

## 프라이빗 네트워크 내 MCP 서버를 인터넷에 공개하지 않고 연결할 수 있는 「Secure MCP Tunnel」 제공 시작  
프라이빗 네트워크 내의 MCP 서버를 인터넷에 공개하지 않고도 OpenAI 서비스에 연결할 수 있는 「Secure MCP Tunnel」을 제공한다.
  
[Secure MCP Tunnel](https://developers.openai.com/api/docs/guides/secure-mcp-tunnels )을 사용하면, 서버를 퍼블릭 인터넷에 공개하거나 방화벽의 인바운드 포트를 개방하지 않고도, 프라이빗 MCP 서버를 ChatGPT, Codex, Responses API 등 지원되는 OpenAI 제품에 연결할 수 있다. 프라이빗 네트워크 내에서 `tunnel-client`를 실행하면 `api.openai.com:443`으로, 또한 컨트롤 플레인의 mTLS가 설정되어 있는 경우에는 `mtls.api.openai.com:443`을 향해 외부 방향의 HTTPS 요청이 전송되며, 프라이빗 MCP 서버와의 통신이 가능해진다.  
  
OpenAI 제품이 OpenAI가 호스팅하는 터널 서비스를 호출하면, 내부 네트워크에서 기동 중인 `tunnel-client`가 큐에 등록된 태스크를 롱 폴링(long polling)을 통해 취득하고, 동일한 터널을 통해 MCP 응답을 반환한다.  
  
`tunnel-client`는 OpenAI API Platform의 [tunnel 설정](https://platform.openai.com/settings/organization/tunnels)에서 다운로드할 수 있는 것 외에, [GitHub](https://github.com/openai/tunnel-client )에서도 최신 버전이 공개되어 있다. 연결에 필요한 `tunnel_id`나 `tunnel-client`의 API 키도 위의 tunnel 설정에서 입수 및 설정할 수 있다. `tunnel-client`의 실행을 확인한 후, ChatGPT 측에서 커넥터 설정을 열고 커스텀 커넥터를 생성하여 「연결」 아래에 있는 「터널」을 선택하면 이용 가능한 터널 목록이 표시되며, 원하는 MCP 서버에 연결하는 터널을 선택할 수 있다.
