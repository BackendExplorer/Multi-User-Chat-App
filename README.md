# 🌐オンラインチャットサービス💬  

## 🖥 シミュレーション



https://github.com/user-attachments/assets/f21b143b-4b1c-4df0-8399-a48202bb3576




## 📌 目次
**このドキュメントの目次です。各セクションへジャンプできます。**

<hr style="border: 3px solid black;">

## **📎 概要**
- [概要](#概要)
- [シミュレーション](#シミュレーション)
- [実行方法](#実行方法)

---

## **🛠 技術関連**
- [使用技術](#使用技術)
- [機能一覧](#機能一覧)
- [クラス図](#クラス図)

---

## **📍開発のポイント**
- [こだわりのポイント](#こだわりのポイント)
- [苦労した点](#苦労した点)
- [さらに追加したい機能](#さらに追加したい機能)

---

## **📄 参考情報**
- [参考文献](#参考文献)

---

## 🍀 概要
ユーザがルームを作成または参加して、グループチャットができるサービス

## 🖥 シミュレーション

## ▶️ 実行方法

## 🛠 使用技術

| カテゴリ | 技術スタック |
|----------|------------|
| 開発言語 | Python 3.13.2 |
| インフラ | Docker |
| その他 | Git, GitHub |


## ⚙ 機能一覧

## 📦 クラス図

### 1. server.pyのクラス図
```mermaid
classDiagram
    direction LR

    TCPServer -- UDPServer

    class TCPServer {
        -HEADER_MAX_BYTE: int
        -TOKEN_MAX_BYTE: int
        -server_address: str
        -server_port: int
        -sock: socket
        -room_members_map: dict
        -clients_map: dict
        +__init__(server_address: str, server_port: int)
        +start_tcp_server(): None
        -accept_tcp_connections(): None
        -handle_client_request(connection: socket, client_address: tuple): None
        -decode_message(data: bytes): tuple
        -register_client(token: bytes, client_address: tuple, room_name: str, payload: str, operation: int): None
        -create_room(connection: socket, room_name: str, payload: str, token: bytes): None
        -join_room(connection: socket, room_name: str, payload: str, token: bytes): None
    }

    class UDPServer {
        -server_address: str
        -server_port: int
        -room_members_map: dict
        -clients_map: dict
        -sock: socket
        +__init__(server_address: str, server_port: int)
        +start_udp_server(): None
        -handle_messages(): None
        -decode_message(data: bytes): tuple
        -broadcast_message(room_name: str, message: str): None
        -remove_inactive_clients(): None
        -disconnect_inactive_client(client_token: bytes, client_info: list): None
    }
```
### 2. client.pyのクラス図
```mermaid
classDiagram
    direction LR

    TCPClient -- UDPClient

    class TCPClient {
        -server_address: str
        -server_port: int
        -sock: socket
        -client_info: dict
        +__init__(server_address: str, server_port: int)
        +start_tcp_client(): dict
        -connect_to_server(): None
        -input_user_name(): str
        -input_operation(): int
        -create_room(username: str): tuple
        -input_room_name(operation: int): str
        -create_packet(room_name: str, operation: int, state: int, payload: str): bytes
        -create_header(room_name: str, operation: int, state: int, payload: str): bytes
        -join_room(username: str): tuple
    }

    class UDPClient {
        -server_address: str
        -server_port: int
        -sock: socket
        -my_info: dict
        -my_token: bytes
        -room_name: str
        +__init__(server_address: str, server_port: int, my_info: dict)
        +start_udp_chat(): None
        -send_username(): None
        -send_message(): None
        -receive_message(): None
        -create_packet(message: bytes = b""): bytes
    }

```


## ✨ こだわりのポイント

## ⚠️ 苦労した点

## 💡 さらに追加したい機能

## 📄 参考文献
<p align="center">
  <img src="https://github.com/user-attachments/assets/5d2a405b-8444-4e73-a5be-d3740a1641d7" width="100%">
</p>


# バイト情報

<table>
  <tr>
    <th>データ名</th>
    <th>説明</th>
    <th>状態</th>
    <th>バイト数</th>
    <th>型</th>
  </tr>
  <tr>
    <td><strong>RoomNameSize</strong></td>
    <td>ルームの許容人数</td>
    <td>-</td>
    <td>1バイト</td>
    <td><code>byte_int (0-255)</code></td>
  </tr>
  <tr>
    <td><strong>Operation</strong></td>
    <td>操作コードのこと</td>
    <td>1：ルームを作成する <br> 2：ルームに参加したい</td>
    <td>1バイト</td>
    <td><code>byte_int (0-255)</code></td>
  </tr>
  <tr>
    <td><strong>State</strong></td>
    <td>現在の状態のこと</td>
    <td>0：サーバの初期化（ルーム作成要求） <br> 1：リクエストの応答（ルーム作成中） <br> 2：リクエストの完了（ルーム作成完了）</td>
    <td>1バイト</td>
    <td><code>byte_int (0-255)</code></td>
  </tr>
  <tr>
    <td><strong>OperationPayloadSize</strong></td>
    <td>データ本体のこと</td>
    <td>-</td>
    <td>29バイト（RoomName: 8バイト + OperationPayload: 21バイト）</td>
    <td><code>byte_str</code></td>
  </tr>
</table>

---

## 詳細仕様
- **新規チャットルーム作成時**
  - 操作コードは `1`
  - 状態は `0 → 1 → 2` の順に推移
  - TCPはトランザクションの完全性を保証するために使用
  - 状態の詳細：
    1. **状態 0（サーバの初期化）**: クライアントがルーム作成リクエストを送信（希望ユーザー名を含む）
    2. **状態 1（リクエストの応答）**: サーバはステータスコードを含むペイロードで即座に応答
    3. **状態 2（リクエストの完了）**: サーバがユニークなトークンをクライアントへ送信（トークンでユーザー名を識別）

- **既存チャットルームへの参加**
  - 操作コードは `2`
  - 状態遷移はルーム作成時と同じ
  - クライアントはトークンを受け取るがホストにはならない

---

## サンプルデータ
```json
{
    "operation": 1,
    "state": 0,
    "roomName": "ChatRoom01"
}
```

---

## 文字列の扱いについて
- `RoomName` は **UTF-8** でエンコード/デコードされる
- `OperationPayload` は操作と状態に応じて異なるフォーマット（整数、文字列、JSONなど）でデコードされる可能性がある

```mermaid
graph TD;
    %% Color style definitions (Server:blue, Client:green, Messaging:purple, Exit:red)

    classDef server fill:#e3f2fd,stroke:#1e88e5,stroke-width:2px;       %% server (blue)
    classDef client fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px;       %% client (green)
    classDef messaging fill:#ede7f6,stroke:#6a1b9a,stroke-width:2px;    %% messaging (purple)
    classDef warning fill:#ffebee,stroke:#c62828,stroke-width:2px;      %% exit/timeout (red)

    %% --- Server Startup ---
    subgraph サーバー起動
        A1(Main Thread) -->|Start TCP Server| A2(TCP Server Thread)
        A1 -->|Start UDP Server| A3(UDP Server Thread)
        A2 -->|Waiting for Client| A4(Client Connection Accepted)
        A4 -->|Create/Join Room| A5(Register Client)
        A3 -->|Message Handling| A6(Message Thread)
        A3 -->|Inactive Monitoring| A7(Monitor Thread)
        class A1,A2,A3,A4,A5,A6,A7 server;
    end

    %% --- Client Startup & Connection ---
    subgraph クライアント起動とサーバー接続
        B1(Client Run) -->|Start TCP Client| B2(TCP Client)
        B2 -->|Connect Server| B3(Connection Success)
        B3 -->|Enter Username| B4(Get Username)
        B4 -->|Select Operation| B5(Create or Join Room)
        B5 -->|Create Room| B6(Create Room Process)
        B6 -->|Send to Server| B7(Receive Token)
        B5 -->|Join Room| B8(Join Room Process)
        B8 -->|Send to Server| B9(Receive Token)
        B2 -->|Start UDP Client| B10(UDP Client)
        B10 -->|Send Room Info| B11(Start Chat)
        class B1,B2,B3,B4,B5,B6,B7,B8,B9,B10,B11 client;
    end

    %% --- Server Messaging ---
    subgraph サーバーでのメッセージ処理
        C1(UDP Server) -->|Receive Message| C2(Receive from Client)
        C2 -->|Parse & Broadcast| C3(Broadcast to Room)
        C1 -->|Monitor Inactivity| C4(Inactive Check)
        C4 -->|Timeout| C5(Kick & Manage Room)
        class C1,C2,C3,C4,C5 messaging;
    end

    %% --- Client Exit ---
    subgraph クライアント退出処理
        D1(Client) -->|User typed exit| D2(UDP 'exit!' Message)
        D2 -->|Close Socket| D3(Program End)
        D1 -->|Server Timeout Notice| D4(Timeout Removal)
        D4 -->|Delete Room or Notify| D5(Server Management)
        class D1,D2,D3,D4,D5 warning;
    end

    %% --- Layout Improvement ---
    A1 --> B1
    B1 --> C1
    C1 --> D1
```
