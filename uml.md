# uml

puml语法：[https://www.heqiangfly.com/2017/07/08/development-tool-markdown-plant-uml/](https://www.heqiangfly.com/2017/07/08/development-tool-markdown-plant-uml/)

```puml
Title "Transport uml"

namespace apache.thrift.transport {
    interface TTransport
    abstract TTransportDefaults
    abstract TVirtualTransport
    abstract TBufferBase
    abstract TTransportFactory
    class TSocket
    TTransport <|.. TTransportDefaults
    TTransportDefaults <|.. TVirtualTransport
    TVirtualTransport <|-- TSocket
    TVirtualTransport <|-- TBufferBase
    TVirtualTransport <|-- TBufferedTransport
    TTransportFactory <|-- TBufferedTransportFactory
    TVirtualTransport <|-- TFramedTransport
    TTransportFactory <|-- TFramedTransportFactory
    TVirtualTransport <|-- TMemoryBuffer
}

interface apache.thrift.transport.TTransport {
    + virtual bool isOpen() const
    + virtual bool peek()
    + virtual void open()
    + virtual void close()
    + virtual void read()
    + virtual void read_virt()
    + virtual void readAll()
    + virtual void readAll_virt()
    + virtual void readEnd()
    + virtual void write()
    + virtual void write_virt()
    + virtual void writeEnd()
    + virtual void flush()
    + virtual void borrow()
    + virtual void borrow_virt()
    + virtual void consume()
    + virtual void consume_virt()
    + virtual void getOrigin() cosnt
    # TTransport() = default
}

abstract apache.thrift.transport.TTransportDefaults {
    + uint32_t read(uint8_t* buf, uint32_t len)
    + uint32_t readAll(uint8_t* buf, uint32_t len)
    + void write(const uint8_t* buf, uint32_t len)
    + const uint8_t* borrow(uint8_t* buf, uint32_t* len)
    + void consume(uint32_t len)
    # TTransportDefaults() = default
}

abstract apache.thrift.transport.TVirtualTransport << Transport_, Super_ = TTransportDefaults >> {
    + uint32_t read_virt(uint8_t* buf, uint32_t len) override
    + uint32_t readAll_virt(uint8_t* buf, uint32_t len) override
    + void write_virt(const uint8_t* buf, uint32_t len) override
    + const uint8_t* borrow_virt(uint8_t* buf, uint32_t* len) override
    + void consume_virt(uint32_t len) override
    + uint32_t readAll(uint8_t* buf, uint32_t len)
    # TVirtualTransport() = default
    # TVirtualTransport(Arg_ const& arg)
}

class apache.thrift.transport.TSocket {
    + bool isOpen() const override
    + bool peek() override
    + void open() override
    + void close() override
    + virtual bool hasPendingDataToRead()
    + virtual uint32_t read(uin8_t* buf, uint32_t len)
    + virtual void write(const uint8_t* buf, uint32_t len)
    + virtual uint32_t write_partial(const uint8_t* buf, uint32_t len)
    + std::string getHost()
    + int getPort()
    + void setHost(std::string host)
    + void setPort(int port)
    + void setLinger(bool on, int linger)
    + void setNoDelay(bool noDelay)
    + void setConnTimeout(int ms)
    + void setRecvTimeout(int ms)
    + void setSendTimeout(int ms)
    + void setMaxRecvRetries(int maxRecvRetries)
    + void setKeepAlive(bool keepAlive)
    + std::string getSocketInfo() const
    + std::string getPeerHost() const
    + std::string getPeerAddress() const
    + int getPeerPort() const
    + THRIFT_SOCKET getSocketFD()
    + void setSocketFD(THRIFT_SOCKET fd)
    + sockaddr* getCachedAddress(socklen_t* len) const
    + static void setUseLowMinRto(bool useLowMinRto)
    + static bool getUseLowMinRto()
    + const std::string getOrigin() const override
    + void setCachedAddress(const sockaddr* addr, socklen_t len)
    + TSocket(THRIFT_SOCKET socket)
    + TSocket(THRIFT_SOCKET socket, std::shared_ptr<THRIFT_SOCKET> interruptListener)
    + TSocket()
    + TSocket(const std::string& host, int port)
    + TSocket(const std::string& path)
    + ~TSocket() override
}

abstract apache.thrift.transport.TBufferBase << TVirtualTransport<TBUfferBase> >> {
    + uint32_t read(uint8_t* buf, uint32_t len)
    + uint32_t readAll(uint8_t* buf, uint32_t len)
    + void write(const uint8_t* buf, uint32_t len)
    + const uint8_t* borrow(uint8_t* buf, uint32_t* len)
    + void consume(uint32_t len)
    # {abstract} virtual uint32_t readSlow(uint8_t* buf, uint32_t len) = 0
    # {abstract} virtual void writeSlow(const uint8_t* buf, uint32_t len) = 0
    # {abstract} virtual const uint8_t* borrowSlow(uint8_t* buf, uint32_t* len) = 0
    # void setReadBuffer(uint8_t* buf, uint32_t len)
    # void setWriteBuffer(uint8_t* buf, uint32_t len)
    # ~TBufferBase() override = default
}

class apache.thrift.transport.TBufferedTransport << TVirtualTransport<TBufferedTransport, TBufferBase> >> {
    + {static} cosnt int DEFAULT_BUFFER_SIZE = 512
    + TBufferedTransport(std::shared_ptr<TTransport> transport)
    + TBufferedTransport(std::shared_ptr<TTransport> transport, uint32_t sz)
    + TBufferedTransport(std::shared_ptr<TTransport> transport, uint32_t rsz, uint32_t wsz)
    + void open() override
    + bool isOpen() const override
    + bool peek() override
    + void close() override
    + uint32_t readSlow(uint8_t* buf, uint32_t len) override;
    + void writeSlow(const uint8_t* buf, uint32_t len) override
    + void flush() override
    + const std::string getOrigin() const override 
    + const uint8_t* borrowSlow(uint8_t* buf, uint32_t* len) override
    + std::shared_ptr<TTransport> getUnderlyingTransport()
    + uint32_t readAll(uint8_t* buf, uint32_t len)
    - std::shared_ptr<TTransport> transport_;
    - uint32_t rBufSize_;
    - uint32_t wBufSize_;
    - boost::scoped_array<uint8_t> rBuf_;
    - boost::scoped_array<uint8_t> wBuf_;
}

abstract apache.thrift.transport.TTransportFactory {
    + {abstract} virtual std::shared_ptr<TTransport> getTransport(std::shared_ptr<TTransport> trans)
}

class apache.thrift.transport.TBufferedTransportFactory {
    + std::shared_ptr<TTransport> getTransport(std::shared_ptr<TTransport> trans) override
}

class apache.thrift.transport.TFramedTransport << TVirtualTransport<TFramedTransport, TBufferBase> >> {
    + {static} static const int DEFAULT_BUFFER_SIZE = 512
    + {static} static const int DEFAULT_MAX_FRAME_SIZE = 256 * 1024 * 1024
    + void open() override
    + bool isOpen() const override
    + bool peek() override
    + void close() override
    + uint32_t readSlow(uint8_t* buf, uint32_t len) override
    + void writeSlow(const uint8_t* buf, uint32_t len) override
    + void flush() override
    + uint32_t readEnd() override
    + uint32_t writeEnd() override
    + const uint8_t* borrowSlow(uint8_t* buf, uint32_t* len) override
    + std::shared_ptr<TTransport> getUnderlyingTransport()
    + const std::string getOrigin() const override
    + void setMaxFrameSize(uint32_t maxFrameSize)
    + uint32_t getMaxFrameSize()
}

class apache.thrift.transport.TFramedTransportFactory {
    + std::shared_ptr<TTransport> getTransport(std::shared_ptr<TTransport> trans) override
}

class apache.thrift.transport.TMemoryBuffer {
    + {static} static const uint32_t defaultSize = 1024
    + enum MemoryPolicy { OBSERVE = 1, COPY = 2, TAKE_OWNERSHIP = 3 }
    + bool isOpen() const override
    + bool peek() override
    + void getBuffer(uint8_t** bufPtr, uint32_t* sz)
    + std::string getBufferAsString()
    + void appendBufferToString(std::string& str)
    + void resetBuffer()
    + void resetBuffer(uint8_t* buf, uint32_t sz, MemoryPolicy policy = OBSERVE)
    + void resetBuffer(uint32_t sz)
    + std::string readAsString(uint32_t len)
    + uint32_t readAppendToString(std::string& str, uint32_t len)
    + uint32_t readEnd() override
    + std::string readAsString(uint32_t len)
    + uint32_t readAppendToString(std::string& str, uint32_t len)
    + uint32_t readEnd() override
    + uint32_t writeEnd() override
    + uint32_t available_read() const
    + uint32_t available_write() const
    + uint8_t* getWritePtr(uint32_t len)
    + void wroteBytes(uint32_t len)

    # uint8_t* buffer_
    # uint32_t bufferSize_
    # uint32_t maxBufferSize_
    # bool owner_
}
```
