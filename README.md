# Bài 3: Viết Dockerfile 3 dòng đóng gói trang Web HTML tĩnh

## Mục tiêu
Tự đóng gói một trang HTML tĩnh thành Docker Image từ `nginx:alpine`.

## Tệp nộp
- `index.html` — nội dung `<h1>Hello Docker Session 09!</h1>`
- `Dockerfile` — gồm 2 câu lệnh:
  ```dockerfile
  FROM nginx:alpine
  COPY index.html /usr/share/nginx/html/index.html
  ```
- `README.md` — tệp này.

## Các lệnh đã thực hiện

```bash
# Build image
docker build -t my-html-app:v1 .

# Khởi chạy container trên cổng 8081
docker run -d -p 8081:80 --name html-app my-html-app:v1

# Kiểm tra
curl http://localhost:8081

# Dọn dẹp (sau khi đã kiểm tra xong)
docker stop html-app && docker rm html-app
```

## Kết quả kiểm tra

```
$ curl http://localhost:8081
<h1>Hello Docker Session 09!</h1>
```

Image `my-html-app:v1` build thành công (CONTENT SIZE ~26.3MB, DISK USAGE ~93.6MB — kế thừa lớp `nginx:alpine` + 1 layer COPY).

## Kết luận
- `curl` trả về đúng chuỗi `<h1>Hello Docker Session 09!</h1>` — đạt yêu cầu.
- Đường dẫn HTML mặc định của Nginx Alpine: `/usr/share/nginx/html/index.html`.

## Deploy trên server qua SSH (cổng 8083)

- Lý do đổi cổng: trên server (`chunghoang@100.115.192.85`, Ubuntu 24.04) cổng **8081 và 8082 đều đã bận** (`ss -tln` thấy `0.0.0.0:8081` của Java Spring app và `*:8082`), còn **8083 trống** nên dùng 8083.
- Server chưa có Docker nên đã cài `docker.io` (Engine 29.1.3) qua `apt-get`.
- Các lệnh deploy trên server:

```bash
# Copy file sang server
scp index.html Dockerfile server:ex3/

# Build + chạy trên cổng 8083 (server)
ssh server "cd ex3 && sudo docker build -t my-html-app:v1 ."
ssh server "sudo docker run -d -p 8083:80 --name html-app my-html-app:v1"

# Kiểm tra trên server
curl http://localhost:8083
# -> <h1>Hello Docker Session 09!</h1> (HTTP 200)
```
