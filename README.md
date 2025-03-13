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
graph TD

classDef server fill:#e3f2fd,stroke:#1e88e5,stroke-width:2px
classDef client fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px
classDef messaging fill:#ede7f6,stroke:#6a1b9a,stroke-width:2px
classDef warning fill:#ffebee,stroke:#c62828,stroke-width:2px

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

    class A1,A2,A3,A4,A5,A6,A7 server
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

    B1 -->|Start TCP Client| B2
    B2 -->|Connect Server| B3
    B3 -->|Enter Username| B4
    B4 -->|Select Operation| B5
    B5 -->|Create Room| B6
    B6 -->|Send to Server| B7
    B5 -->|Join Room| B8
    B8 -->|Send to Server| B9
    B2 -->|Start UDP Client| B10
    B10 -->|Send Room Info| B11

    class B1,B2,B3,B4,B5,B6,B7,B8,B9,B10,B11 client
end

subgraph Server Message Handling
    C1(UDP Server)
    C2(Receive from Client)
    C3(Broadcast to Room)
    C4(Inactive Check)
    C5(Kick & Manage Room)

    C1 -->|Receive Message| C2
    C2 -->|Parse & Broadcast| C3
    C1 -->|Monitor Inactivity| C4
    C4 -->|Timeout| C5

    class C1,C2,C3,C4,C5 messaging
end

subgraph Client Exit
    D1(Client)
    D2(UDP 'exit!')
    D3(Program End)
    D4(Timeout Removal)
    D5(Server Management)

    D1 -->|User typed exit| D2
    D2 -->|Close Socket| D3
    D1 -->|Server Timeout Notice| D4
    D4 -->|Delete Room or Notify| D5

    class D1,D2,D3,D4,D5 warning
end

A1 --> B1
B1 --> C1
C1 --> D1
```
