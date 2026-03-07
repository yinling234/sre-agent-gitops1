# 构建阶段：使用 golang alpine 基础镜像
FROM golang:alpine AS builder
WORKDIR /app

# ========== 核心新增：配置容器内 Go 国内代理（解决依赖拉取失败） ==========
# 用七牛云国内代理，彻底绕过 proxy.golang.org
ENV GOPROXY=https://goproxy.cn,direct
# 禁用校验和数据库，避免访问海外 sum.golang.org
ENV GOSUMDB=off
# 强制开启模块模式，确保代理配置生效
ENV GO111MODULE=on

# 1. 先拷贝依赖定义（原逻辑保留）
COPY go.mod go.sum ./
# 2. 再拷贝源代码（原逻辑保留）
COPY main.go .

# 3. 整理依赖（现在会走国内代理，不再访问海外地址）
RUN go mod tidy

# 4. 编译（优化编译参数，生成静态二进制文件，避免运行时依赖）
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -a -installsuffix cgo -o mcp-server .

# 运行阶段：使用轻量 alpine 镜像
FROM alpine:latest
WORKDIR /app
# 复制编译好的二进制文件
COPY --from=builder /app/mcp-server .
# 暴露端口（原逻辑保留）
EXPOSE 8080
# 启动程序（原逻辑保留）
CMD ["./mcp-server"]
