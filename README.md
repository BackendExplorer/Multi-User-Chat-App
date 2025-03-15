# 🌐 オンラインチャットサービス 💬  

## 🖥 シミュレーション








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
- [処理フロー (フローチャート)](#処理フロー)

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

# 📦 クラス図と構成

このプロジェクトは、**TCP/UDP通信を用いたチャットシステム** を構成するクラス群で成り立っています。サーバーとクライアントでそれぞれの役割を持ち、以下のように分類されます。

---

## 1 クラス図

### 1.1 server.py のクラス図
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

### 1.2 client.py のクラス図
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
        -create_packet(message: bytes = b"" ): bytes
    }
```

---# クラス図と構成

このプロジェクトは、**TCP/UDP通信を用いたチャットシステム** を構成するクラス群で成り立っています。サーバーとクライアントでそれぞれの役割を持ち、以下のように分類されます。

---

## クラス図

### server.py のクラス図
```mermaid
classDiagram
    direction LR

    TCPServer -- UDPServer

    class TCPServer {
        - HEADER_MAX_BYTE: int
        - TOKEN_MAX_BYTE: int
        - server_address: str
        - server_port: int
        - sock: socket
        - room_members_map: dict
        - clients_map: dict
        + __init__(server_address: str, server_port: int)
        + start_tcp_server(): None
        - accept_tcp_connections(): None
        - handle_client_request(connection: socket, client_address: tuple): None
        - decode_message(data: bytes): tuple
        - register_client(token: bytes, client_address: tuple, room_name: str, payload: str, operation: int): None
        - create_room(connection: socket, room_name: str, payload: str, token: bytes): None
        - join_room(connection: socket, room_name: str, payload: str, token: bytes): None
    }

    class UDPServer {
        - server_address: str
        - server_port: int
        - room_members_map: dict
        - clients_map: dict
        - sock: socket
        + __init__(server_address: str, server_port: int)
        + start_udp_server(): None
        - handle_messages(): None
        - decode_message(data: bytes): tuple
        - broadcast_message(room_name: str, message: str): None
        - remove_inactive_clients(): None
        - disconnect_inactive_client(client_token: bytes, client_info: list): None
    }
```

### client.py のクラス図
```mermaid
classDiagram
    direction LR

    TCPClient -- UDPClient

    class TCPClient {
        - server_address: str
        - server_port: int
        - sock: socket
        - client_info: dict
        + __init__(server_address: str, server_port: int)
        + start_tcp_client(): dict
        - connect_to_server(): None
        - input_user_name(): str
        - input_operation(): int
        - create_room(username: str): tuple
        - input_room_name(operation: int): str
        - create_packet(room_name: str, operation: int, state: int, payload: str): bytes
        - create_header(room_name: str, operation: int, state: int, payload: str): bytes
        - join_room(username: str): tuple
    }

    class UDPClient {
        - server_address: str
        - server_port: int
        - sock: socket
        - my_info: dict
        - my_token: bytes
        - room_name: str
        + __init__(server_address: str, server_port: int, my_info: dict)
        + start_udp_chat(): None
        - send_username(): None
        - send_message(): None
        - receive_message(): None
        - create_packet(message: bytes = b"" ): bytes
    }
```

---

## クラスの構成

### サーバープログラム
サーバー側のクラスは、**クライアントの接続管理、リクエスト処理、ルーム管理** などを担当します。

#### TCPServer
**概要**  
TCP通信を介してクライアントからのリクエストを受け取り、適切な処理を実行します。

| 機能 | 説明 |
|------|------|
| クライアントからの接続受付 | `start_tcp_server()` |
| リクエスト処理 | `handle_client_request()` |
| クライアント情報の登録 | `register_client()` |
| ルームの作成 | `create_room()` |
| ルームへの参加 | `join_room()` |
| メッセージの解析 | `decode_message()` |

#### UDPServer
**概要**  
UDP通信を介してメッセージを受信し、リレーまたは適切な処理を行います。

| 機能 | 説明 |
|------|------|
| クライアントからのメッセージ受信 | `handle_messages()` |
| メッセージの処理 | `decode_message()` |
| ルーム内のメンバーにメッセージをブロードキャスト | `broadcast_message()` |
| 非アクティブクライアントの削除 | `remove_inactive_clients()` |

---

### クライアントプログラム
クライアント側のクラスは、**サーバーとの通信、メッセージ送受信、ユーザーインターフェース** を担当します。

#### TCPClient
**概要**  
TCP通信を介してサーバーにリクエストを送信し、レスポンスを受信します。

| 機能 | 説明 |
|------|------|
| サーバー接続 | `connect_to_server()` |
| ルームの作成 | `create_room()` |
| ルームへの参加 | `join_room()` |
| ルーム一覧の取得 | `input_room_name()` |
| パケット作成 | `create_packet()` |

#### UDPClient
**概要**  
UDP通信を介してメッセージを送受信します。

| 機能 | 説明 |
|------|------|
| ユーザー名の送信 | `send_username()` |
| メッセージの送信 | `send_message()` |
| メッセージの受信 | `receive_message()` |
| パケット作成 | `create_packet()` |

---

## 処理フロー (フローチャート)

```mermaid
graph TD

subgraph Server Startup
    A1(Main Thread)
    A2(TCP Server Thread)
    A3(UDP Server Thread)
    A4(Client Connection Accepted)
    A5(Register Client)
    A6(Message Thread)
    A7(Monitor Thread)
    A1 -->|Start TCP Server| A2
    A1 -->|Start UDP Server| A3
    A2 -->|Waiting for Client| A4
    A4 -->|Create/Join Room| A5
    A3 -->|Message Handling| A6
    A3 -->|Inactive Monitoring| A7
end

subgraph Client Startup
    B1(Client Run)
    B2(TCP Client)
    B3(Connection Success)
    B4(Get Username)
    B5(Create or Join Room)
    B6(Create Room Process)
    B7(Receive Token)
    B8(Join Room Process)
    B9(Receive Token)
    B10(UDP Client)
    B11(Start Chat)
    B1 --> B2
    B2 --> B3
    B3 --> B4
    B4 --> B5
end

A1 --> B1
B1 --> C1
C1 --> D1
```




## ✨ こだわりのポイント

## ⚠️ 苦労した点

## 💡 さらに追加したい機能

## 📄 参考文献
<p align="center">
  <img src="https://github.com/user-attachments/assets/43766c52-7a68-4d7e-852d-18bf48755f78" width="100%">
</p>


# バイト情報




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

