# TCP 的运输连接管理

## 一、介绍

TCP（Transmission Control Protocol，传输控制协议）是一种面向连接的协议，它在通信双方之间建立可靠的连接，以确保数据的有序、无差错传输。TCP 的连接使用**三次握手**机制，TCP 连接的释放使用**四次挥手**机制。

在 TCP 连接建立过程中，要解决三个问题：

1. 要使每一方都能够知道对方的存在。
2. 要允许双方协商一些参数。
3. 能够对运输实体资源（如缓存大小、连接表中的项目等）进行分配。

## 二、TCP 的连接建立

### 2.1 TCP 的连接建立的过程

TCP 在客户端和服务器端建立连接的过程称为**三次握手**。以下是三次握手过程的示例图。

![TCP 的三次握手过程](../assets/tcp-three-way-handshake.png)
*图片来自 《计算机网络（第七版）》*

1. **初始状态**：客户端和服务器都处于 CLOSED（关闭）状态，此时，服务器处于 LISTEN（收听）状态。

2. **第一次握手（SYN）**：客户端向服务器发出连接请求报文段，表示希望建立连接（报文格式如下所示）。此时，客户端进入 SYN-SENT（同步已发送）状态。

    ```plaintext
    SYN=1, seq=x
    ```

3. **第二次握手（SYN-ACK）**：服务器收到连接请求报文段后，如果同意建立连接，则向客户端发送确认报文段（确认报文格式如下所示）。此时，服务器处于 SYN-RCVD（同步已收到）状态。

    ```plaintext
    SYN=1, ACK=1, seq=y, ack=x+1
    ```

4. **第三次握手（ACK）**：客户端收到服务器的确认后，还要向服务器发送一个确认报文（确认报文格式如下所示）。此时，TCP 连接已经建立，客户端进入 ESTABLISHED（已建立连接） 状态。服务器收到客户端的确认后，也进入 ESTABLISHED 状态。

    ```plaintext
    ACK=1, seq=x+1, ack=y+1
    ```

### 2.2 为什么要进行第三次握手？

上面的过程中，客户端最后又发送了一次确认，原因是为了**防止已失效的连接请求，突然又传到了服务器，因而产生错误**。

这里所说的**已失效的连接请求**，是指这样一种情况。举例来说，客户端刚发送了一次连接请求 `A`，但是由于种种原因，`A` 在网络中的某个节点被滞留了，以致延误到连接释放以后的某个时间节点才到达服务器。此时，`A` 请求应该被认定为失效的请求，但之后 `A` 请求到达了服务器，服务器认为客户端又发出了一次连接请求，于是，服务器向客户端发出确认报文，同意建立连接。如果没有第三次握手，那么此时新的连接就已经建立了。

然而，实际的情况是，客户端并没有发出连接请求，所以，客户端会忽略服务器的确认报文，也就不会向服务器发送数据。但服务器认为连接已经建立，会等待客户端发送数据，这样，就造成了服务器资源的浪费。

采用三次握手，就可以防止上述情况的发生。也就是上边的例子中，客户端不会向服务器的确认发送确认报文（即第三次握手的过程不会发生）。由于服务器没有收到客户端的确认报文，也就知道了，客户端并没有要求建立连接。

### 2.3 常见问题及处理

SYN 洪泛攻击（SYN Flood Attack）是利用 TCP 三次握手过程中的漏洞进行的拒绝服务攻击。攻击者发送大量伪造的 SYN 报文，使服务器创建大量半连接，耗尽服务器资源。

以下是防御方法。

- **SYN Cookies**：在服务器生成 SYN-ACK 报文时，不立即分配资源，而是通过加密算法生成一个 Cookie 作为 ACK 报文的一部分。客户端在第三次握手时，发送包含此 Cookie 的 ACK 报文，服务器验证后才分配资源。
- **缩短 SYN-RECEIVED 超时时间**：减少服务器保持半连接状态的时间，尽快释放资源。
- **限制每个 IP 的连接数**：防止单个 IP 发送大量 SYN 报文。

## 三、TCP 的连接释放

### 3.1 TCP 的连接释放的过程

**四次挥手**（Four-Way Handshake）是 TCP 协议中用于终止连接的过程。它包括四个步骤，确保客户端和服务器双方都能正确地终止连接，并且确认对方已准备好断开连接。以下是四次挥手过程的示例图。

![TCP 的四次挥手过程](../assets/tcp-four-way-handshake.png)
*图片来自 《计算机网络（第七版）》*

1. **初始状态**：客户端处于 ESTABLISHED 状态，准备断开 TCP 连接；服务器处于 ESTABLISHED 状态，与客户端保持连接。

2. **第一次挥手（FIN）**：**客户端**向服务器发送一个 FIN（Finish）报文，表示希望终止连接。此时，客户端进入 FIN-WAIT-1（终止等待 1）状态，等待服务器的确认。

    ```plaintext
    FIN=1, seq=u
    ```

3. **第二次挥手（ACK）**：
    - **服务器**：收到客户端的 FIN 报文后，向客户端发送一个确认报文（ACK），确认已收到客户端的 FIN 报文。此时，服务器进入 CLOSE-WAIT（关闭等待）状态。到目前为止，从客户端到服务器这个方向的连接就释放了，这时的 TCP 连接处于**半关闭状态**，即客户端已经没有数据要发送了，但服务器若发送数据，客户端仍要接受。也就是说，从服务器到客户端这个方向的连接未关闭。

        ```plaintext
        ACK=1, seq=v, ack=u+1
        ```

    - **客户端**：收到服务器的确认后，进入 FIN-WAIT-2（终止等待 2）状态，等待服务器发出的连接释放报文段。

4. **第三次挥手（FIN）**：**服务器**向客户端发出释放连接报文段，表示希望终止连接。这时，服务器进入 LAST-ACK（最后确认）状态，等待客户端的确认。

    ```plaintext
    FIN=1, ACK=1, seq=w, ack=u+1
    ```

5. **第四次挥手（ACK）**：
    - **客户端**：收到服务器连接释放报文段后，向服务器发送一个确认报文，确认已收到服务器的释放请求。此时，客户端进入 TIME-WAIT（时间等待）状态。

        ```plaintext
        ACK=1, seq=u+1, ack=w+1
        ```

    - **服务器**：收到客户端的 ACK 报文后，进入 CLOSED 状态，连接正式关闭。

    - **客户端**：注意，此时 TCP 连接并未释放，通常，客户端要在 TIME-WAIT 状态等待一段时间（一般为2倍的最大报文生存时间，**2MSL**）后，进入 CLOSED 状态，连接正式关闭。

### 3.2 为什么客户端要在 TIME-WAIT 状态等待 2MSL？

1. **确保最后一个 ACK 报文能够到达服务器**：如果服务器没有收到最后的 ACK 报文，它会重传 FIN 报文，而客户端在 TIME-WAIT 状态可以重新发送 ACK 报文。

    为了保证客户端发送的最后一个 ACK 报文段能够到达服务器。这个 ACK 报文段有可能丢失，因而使处在 LAST-ACK 状态的服务器收不到对已经发送的 FIN + ACK 报文段的确认。服务器会超时重传这个 FIN + ACK 报文段，而客户端就能在 2MSL 时间内收到这个重传的 FIN + ACK 报文段。接着客户端重传一次确认，重新启动 2MSL 计时器。最后，客户端和服务器都正常进入到 CLOSED 状态。如果客户端在 TIME-WAIT 状态不等待一段时间，而是在发送完 ACK 报文段后立即释放连接，那么就无法收到服务器重传的 FIN + ACK 报文段，因而也不会再发送一次确认报文段。这样，服务器就无法按照正常步骤进入 CLOSED 状态。

2. **防止旧连接的数据影响新连接**：在 TIME-WAIT 状态，确保旧连接中的所有报文在网络中消失，避免影响同一端口的新连接。

    也就是防止之前提到的“已失效的连接请求报文段”出现在本连接中。客户端在发送完最后一个 ACK 报文段后，再经过时间 2MSL，就可以使本连接持续的时间内所产生的所有报文段都从网络中消失。这种将可以使下一个新连接中不会出现这种旧的连接请求报文段。

    服务器只要收到了客户端发出的确认，就进入 CLOSED 状态。同样，服务器在撤销相应的传输控制块 TCB 后，就结束了这次的 TCP 连接。我们注意到，服务器结束 TCP 连接的时间要比客户端早一些。

上述的 TCP 连接释放过程是四次挥手。

除时间等待计时器外，TCP 还设有一个保活计时器 (keepalive timer)。设想有这样的情况：客户已主动与服务器建立了 TCP 连接。但后来客户端的主机突然出故障。显然，服务器端以后就不能再收到客户发来的数据。因此，应当有措施使服务器不再白白等待下去。这就是使用保活计时器。服务器每收到一次客户的数据信息，就重新设置保活计时器，时间的设置通常是两小时。若两小时没有收到客户的数据信息，服务器就发送一个探测报文段，以后则每隔 75 秒钟发送一次。若一连发送 10 个探测报文段仍无客户的响应，服务器就认为客户端出了故障，接着就关闭这个连接。

### 3.3 常见问题及处理

#### （1）TIME-WAIT 状态过多

在高并发的网络服务中，大量的 TIME-WAIT 状态可能会耗尽系统资源。

- **优化方法**：
  - **增加端口范围**：增加系统可用的临时端口范围，减少端口耗尽的概率。
  - **缩短 TIME-WAIT 超时时间**：在操作系统中配置缩短 TIME-WAIT 状态的持续时间（仅在确保网络环境稳定的情况下进行）。
  - **复用端口**：启用端口复用选项，使得在 TIME-WAIT 状态可以复用端口（如设置 `SO_REUSEADDR` 选项）。

#### （2）半关闭状态

在四次挥手过程中，服务器在发送 FIN 报文之前可能会继续发送数据，而客户端需要继续接收这些数据。这种状态称为半关闭状态。

- **处理方法**：
  - **正确处理半关闭状态**：确保在连接关闭之前，所有数据都已成功传输。
  - **使用双工关闭**：在某些情况下，可以通过应用层协议处理双工关闭，即双方同时发送 FIN 报文。

通过正确理解 TCP 的四次挥手机制，开发者可以更好地管理网络连接，确保数据传输的可靠性和安全性。

参考

- 《计算机网络（第七版），谢希仁》