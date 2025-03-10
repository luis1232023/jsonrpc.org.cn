## JSON-RPC 2.0 规范

<span style="font-size: 12px;">开始日期:2010-03-26 <span style="font-size: 12px;">(based on the 2009-05-24 version)</span>
<span style="font-size: 12px;">更新:2013-01-04</span>
<span style="font-size: 12px;">作者:JSON-RPC Working Group <json-rpc@googlegroups.com></span>

1. #### 概述
    JSON-RPC 是一种轻量级的远程过程调用协议（RPC），使用 JSON 格式（RFC 4627）作为数据交换标准，支持跨多种传输环境（如 HTTP、Socket）。该协议设计为无状态、简单明了。

2. #### 术语
    - 文档中用到的关键术语如 "MUST"（必须）、"SHALL NOT"（禁止）等需按照 RFC 2119 的定义解释。
    -  JSON-RPC 的数据结构包括基本类型（字符串、数字、布尔值、Null）和结构化类型（对象、数组）。
    -  客户端发送请求对象，服务器返回响应对象。

3. #### 兼容性
    JSON-RPC 2.0 与 1.0 版本不完全兼容，2.0 引入了 jsonrpc 字段（值为 "2.0"）作为版本标识。大部分实现应尽量兼容 1.0。

4. #### 请求对象
    一个jsonrpc调用通过向服务器发送Request对象来表示。
    - 请求对象(Request）有如下成员：
    a. jsonrpc: 一个字符串，表示JSON-RPC协议版本。
    b. method: 一个字符串，表示要调用的方法名称。
    c. params: 一个可选的参数数组或对象。
    d. id: 一个可选的字符串、数字或null，用于唯一标识请求。
    例如：
        ```json
        {
            "jsonrpc": "2.0",
            "method": "subtract",
            "params": [42, 23],
            "id": 1
        }
        ```
    - 如果是通知（notification），则可以不包含id字段。
    - 参数结构
    如果存在，rpc调用的参数必须以结构化值的形式提供。通过数组按位置或通过对象按名称。
    按位置：params必须是一个数组，包含服务器预期顺序的值。
    按名称：params必须是一个对象，其成员名称与服务器期望的参数名称匹配。缺少预期的名称可能会导致生成错误。名称必须与方法的预期参数完全匹配，包括大小写。

5. #### 响应对象
    - 响应对象(Response)有如下成员：
    a. jsonrpc: 一个字符串，表示JSON-RPC协议版本。
    b. result: 一个可选的任何类型的值，表示请求的结果。
    c. error: 一个可选的错误对象，表示请求失败。
    d. id: 一个可选的字符串、数字或null，用于唯一标识请求，和请求id一致。
    例如：
    ```json
    {
        "jsonrpc": "2.0",
        "result": 19,
        "id": 1
    }
    ```
    - 当json-rpc调用遇到错误时，响应对象必须包含错误成员，其值为具有以下成员的对象：
    a. code: 一个数字，表示错误的类型。
    b. message: 一个字符串，描述错误的消息。
    c. data: 一个可选的任何类型的值，提供额外的错误信息。
    例如：
    ```json
    {
        "jsonrpc": "2.0",
        "error": {
            "code": -32601,
            "message": "Method not found",
            "data": null
        },
        "id": 1
    }
    ```
    - 错误代码:
    JSON-RPC 2.0定义了以下错误代码：
    a. 32700: Parse error 服务器接收到无效的JSON。解析JSON文本时，服务器上发生错误。
    b. 32600: Invalid Request  发送的JSON不是有效的Request对象。
    c. 32601: Method not found 该方法不存在/不可用。
    d. 32602: Invalid params 方法参数无效。
    e. 32603: Internal error 内部JSON-RPC错误。
    f. 32000 到 -32099: Server error 保留用于实现定义的服务器错误。

6. #### 批量请求：
    为了同时发送多个请求对象，客户端 可以（MAY） 发送一个包含请求对象的数组。

    服务器应（SHOULD）在处理完所有批量请求对象后，返回一个包含相应响应对象的数组。对于每个请求对象，应该（SHOULD）有一个对应的响应对象，但对于通知（即没有 id 字段的请求对象），则不应该（SHOULD NOT）生成任何响应对象。服务器可以（MAY）将批量 RPC 调用视为一组并发任务来处理，可以以任何顺序并行执行。

    从批量调用返回的响应对象数组中的顺序可以是任意的。客户端应（SHOULD）基于每个对象中的 id 字段，在请求对象集合和结果响应对象集合之间匹配上下文。

    如果批量 RPC 调用本身无法被识别为有效的 JSON 或至少包含一个值的数组，服务器必须（MUST）返回一个单独的响应对象。如果响应数组中没有响应对象（例如通知场景），服务器不得（MUST NOT）返回一个空数组，而是完全不返回任何内容。

7. #### 例子
    - 请求：
    ```json
    // 请求
    {
        "jsonrpc": "2.0", 
        "method": "subtract",
         "params": [42, 23], 
         "id": 1
    }
    // 响应
    {
        "jsonrpc": "2.0", 
        "method": "subtract",
         "params": [42, 23], 
         "id": 1
    }
        // 请求
    {
        "jsonrpc": "2.0", 
        "method": "subtract",
         "params": {
            "subtrahend": 23, 
            "minuend": 42
        }, 
         "id": 2
    }
    // 响应
    {
        "jsonrpc": "2.0", 
        "method": "subtract",
         "params": 19, 
         "id": 2
    }
    ```
    - 通知
    ```json
    // 请求
    {
        "jsonrpc": "2.0", 
        "method": "update",
        "params": [1,2,3,4,5]
    }
    // 响应
    {
        "jsonrpc": "2.0", 
        "method": "foobar",
    }
    ```
    - 批量请求
    ```json
    // 请求
    [
        {
            "jsonrpc": "2.0", 
            "method": "sum",
            "params": [1,2,4], 
            "id": "1"
        }, 
        {
            "jsonrpc": "2.0", 
            "method": "notify_hello",
            "params": [7]
        }, 
        {
            "jsonrpc": "2.0", 
            "method": "subtract",
            "params": [42,23], 
            "id": "2"
        }, 
        {
            "jsonrpc": "2.0", 
            "method": "notify_sum",
            "params": [1,2,3,4,5], 
            "id": null
        }
    ]
    // 响应
    [
        {
            "jsonrpc": "2.0", 
            "result": 7, 
            "id": "1"
        }, 
        {
            "jsonrpc": "2.0", 
            "result": 19, 
            "id": "2"
        }
    ]
    ```