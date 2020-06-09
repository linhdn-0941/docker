# Dockerfile
- Docker có thể build images từ `Dockerfile`.
- `Dockerfile` là một file text chứa các lệnh mà người dùng có thể build một image.
- Sử dụng lệnh `docker build` để tạo image bằng cách tự động thực hiện các dòng lệnh liên tiếp trong `Dockerfile`.
# Usage
- Lệnh `docker build` builds một image từ một `Dockerfile` và một context.
- Context là tập hợp các tệp tại một vị trí được chỉ định `PATH` or `URL`. `PATH` là một thư mục dưới máy local. `URL` là link repository trên docker hub.
```docker
docker build .
```
- Lệnh trên có nghĩa là build image từ Dockerfile trong thư mục hiện tại.
---
**NOTE**
- Bản build được thực thi bởi daemon Docker(Chạy ở trên host, đóng vai trò server, nhận các RESTful requests từ Docker Client và thực thi.), không phải bởi CLI
- Nên bắt đầu với một thư mục trống làm context và để Dockerfile của bạn trong thư mục đó.
- Chỉ thêm các file cần thiết để  build Dockerfile.
- Không được sử  dụng thư mục gốc (root) nếu làm như vậy bản build sẽ chuyển toàn bộ nội dung trong ổ cứng của bạn sang daemon Docker.
---
- Để sử dụng một file trong bản build thì sử dụng lệnh `COPY` trong `Dockerfile`
- Loại trừ các file và folder bằng cách thêm một file `.dockerignore` trong context.
# Practices for writing Dockerfiles
```docker
FROM ubuntu:18.04
COPY . /app
CMD python /app/app.py
```
- `FROM` tạo một layer từ image `ubuntu:18.04`
- `COPY` Thêm tất cả các file từ thư mục hiện tại.
- `CMD` Chỉ định lệnh nào để chạy trong container.

- `FROM` Sử dụng image làm cơ sở cho image của bạn, khuyến cáo nên sử  dụng Alpine image vì nó kiểm soát chặt chẽ, kích cỡ nhỏ(dưới 5MB), trong khi vẫn là một bản phân phối Linux đầy đủ.
```docker
FROM [--platform=<platform>] <image> [AS <name>]
FROM [--platform=<platform>] <image>[:<tag>] [AS <name>]
FROM [--platform=<platform>] <image>[@<digest>] [AS <name>]
```
- `LABEL` Bạn có thể thêm label vào image của mình để giúp tổ chức image theo dự án, hồ sơ thông tin cấp phép, để hỗ trợ tự động hóa, hoặc vì lý do khác.
```BASH
LABEL com.example.version="0.0.1-beta"
LABEL vendor1="ACME Incorporated"
LABEL vendor2=ZENITH\ Incorporated
LABEL com.example.release-date="2015-02-12"
LABEL com.example.version.is-production=""
```
- `RUN` Câu lệnh trên nhiều dòng được phân tách bằng dấu gạch chéo  ngược '\' để làm cho `Dockerfile` dễ đọc hơn, dễ hiểu hơn và có thể duy trì.
```bat
RUN apt-get update && apt-get install -y \
    package-bar \
    package-baz \
    package-foo
```
- `CMD` Nên được sử dụng để chạy phần mềm có trong image của bạn, cùng với mọi đối số.
```bat
CMD ["executable","param1","param2"]
CMD ["param1","param2"]
CMD command param1 param2
```
- `EXPOSE` Chỉ ra các cổng mà container lắng nghe các kết nối,bạn nên sử dụng cổng truyền thống, phổ biến cho ứng dụng của mình VD: Mysql 3306, MongoDB 27017 ...
```bat
EXPOSE <port> [<port>/<protocol>...]
```
- `ENV` Lệnh ENV đặt biến môi trường key value
```bat
ENV myName John Doe
ENV myDog Rex The Dog
ENV myCat fluffy
```
- `ADD  COPY` Mặc dù ADD và COPY giống nhau về mặt chức năng, nói chung, `COPY` được ưu tiên vì nó minh bạch hơn `ADD`. `COPY` chỉ hỗ trợ sao chép cơ bản các tệp cục bộ vào vùng chứa, trong khi ADD có một số tính năng (như trích xuất tar chỉ cục bộ và hỗ trợ URL từ xa) không rõ ràng ngay lập tức.
```bat
COPY requirements.txt /tmp/
ADD http://example.com/big.tar.xz /usr/src/things/
```
- `ENTRYPOINT` Cách sử dụng tốt nhất cho ENTRYPOINT là đặt lệnh chính của image, cho phép image đó được chạy(và sau đó sử dụng CMD làm cờ mặc định).
```bat
ENTRYPOINT ["s3cmd"]
CMD ["--help"]
```
- `VOLUME` Nên được sử dụng để hiển thị bất kỳ vùng lưu trữ cơ sở dữ liệu, lưu trữ cấu hình hoặc tệp / thư mục được tạo bởi bộ chứa docker của bạn.
```bat
VOLUME ["/data"]
```
- `WORKDIR` Định nghĩa thư mục cho CMD
```bat
WORKDIR /path/to/workdir
```
