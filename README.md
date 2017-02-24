socketgo
========

[![GoDoc](https://godoc.org/github.com/nulijiabei/socketgo?status.svg)](https://godoc.org/github.com/nulijiabei/socketgo)

提供 golang socket 本项目是个代码库


# 安装

**自动安装**

	go get github.com/nulijiabei/socketgo
	
**手动安装**

自己手动从 github 下载代码后，放置在你的 $GOPATH 的 src/github.com/nulijiabei/socketgo 目录下

	go install github.com/nulijiabei/socketgo
	

# 使用

	import socket "github.com/nulijiabei/socket"

	tcp := socket.NewTCP("127.0.0.1", "8700", 3)
	return tcp.ReadWrite(func(conn *net.TCPConn) error {
	    // conn.Write()
	    // conn.Read()
	    return nil
	})
	udp := socket.NewUDP("127.0.0.1", "8080", 3)
	udp.ReadWrite(func(conn *net.TCPConn) error {
	    // conn.Write()
	    // conn.Read()
	    return nil
	})

	// 接收客户端发来的所有TCP请求
	func (ma *Master) doReadTcpSla(ch chan int) {

	    // 监听TCP端口等待注册
	    lis, err := socket.NewListen("", "9999", 3).ListenTCP()
	    if err != nil {
		log.Fatalln(err)
	    }
	    // 保证监听正常关闭
	    defer lis.Close()

	    // 等待连接
	    for {
		// 等待 sla 连接
		conn, err := lis.Accept()
		if err != nil {
		    log.Println(err)
		    continue
		}
		// 接收连接线程
		go (func(conn net.Conn) {
		    // 连接交给处理器                
		    err := 处理连接(conn)
		    if err != nil {
			log.Println(err)
		    }
		})(conn)
	    }
	}

	// 监听端口
	func (ma *Master) doPeanut(ch chan int) {

	    // 监听TCP端口等待注册
	    lis, err := socket.NewListen("", "9999", 3).ListenTCP()
	    if err != nil {
		log.Fatalln(err)
	    }
	    // 保证监听正常关闭
	    defer lis.Close()

	    // 等待连接
	    for {
		// 等待 sla 连接
		conn, err := lis.Accept()
		if err != nil {
		    continue
		}
		// 建立读写流
		r := bufio.NewReader(conn)
		w := bufio.NewWriter(conn)
		// 存储返回信息
		datas := make([]byte, 0)
		// 循环内进行读取
		for {
		    // 按行读取
		    data, err := r.ReadBytes('\n')
		    // 遇到结尾跳出
		    if err != nil {
			break
		    }
		    // 保存到数据
		    datas = append(datas, data...)
		}
	    }
	}

	// 向客户端端广播时间戳
	func (ma *Master) doTimeSync(ch chan int) {

	    // 开始
	    for {
		// 建立UDP广播
		conn := socket.NewUDP("255.255.255.255", "8888", 3)
		// 读写
		err := conn.ReadWrite(func(conn *net.UDPConn) error {
		    // 循环广播发送时间戳
		    for {
			// 提取本地时间戳
			_, err := conn.Write([]byte(UnixNano()))
			if err != nil {
			    return err
			}
			// 每10秒发送一次
			time.Sleep(time.Second * 10)
		    }
		    // 不会到这...
		    return nil
		})
		// 踢出错误
		if err != nil {
		    log.Fatalln(err)
		}
	    }
	}
