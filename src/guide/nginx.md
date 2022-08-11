# Learning Nginx

## What Is a Web Server ?

Máy chủ Web (Web Server) là máy tính mà trên đó cài đặt phần mềm phục vụ web, đôi khi người ta cũng gọi chính phần mềm đó là web server.
Máy chủ web lưu trữ và cung cấp nội dung của một trang web - chẳng hạn như văn bản, hình ảnh, video và dữ liệu ứng dụng - cho các khách hàng yêu cầu nó. Loại ứng dụng khách phổ biến nhất là trình duyệt web, trình duyệt này yêu cầu dữ liệu từ trang web của bạn khi người dùng nhấp vào liên kết hoặc tải xuống tài liệu trên trang được hiển thị trong trình duyệt.
Máy chủ web giao tiếp với trình duyệt web bằng Giao thức truyền siêu văn bản (Hypertext Transfer Protocol: HTTP). Nội dung của hầu hết các trang web được mã hóa bằng Ngôn ngữ đánh dấu siêu văn bản (Hypertext Markup Language: HTML). Nội dung có thể tĩnh (ví dụ: văn bản và hình ảnh) hoặc động (ví dụ: giá đã tính hoặc danh sách các mặt hàng mà khách hàng đã đánh dấu để mua). Để cung cấp nội dung động, hầu hết các máy chủ web hỗ trợ ngôn ngữ kịch bản phía máy chủ (server‑side scripting languages) để mã hóa logic nghiệp vụ vào giao tiếp. Các ngôn ngữ được hỗ trợ phổ biến bao gồm Active Server Pages (ASP), Javascript, PHP, Python và Ruby,...

Máy chủ web cũng có thể lưu nội dung vào bộ nhớ cache để tăng tốc độ phân phối nội dung thường được yêu cầu. Quá trình này còn được gọi là tăng tốc web .

## Máy chủ Web có cần Public IP Addresses ?

Để được người dùng bên ngoài truy cập vào mạng của nó, web server cần có Public IP Addresses. Tuy nhiên, để cải thiện bảo mật, các máy chủ web thường được "hidden" sau proxy server or load balancer. Hầu hết các trang web lớn đều có nhiều máy chủ web, với load balancer để phân phối lưu lượng truy cập trên chúng. Nhiều trang web cũng có thể chia sẻ địa chỉ IP công cộng của một máy chủ web và trình cân bằng tải sau đó sử dụng thông tin trong URL yêu cầu để xác định trang web nào nhận được yêu cầu của khách hàng.

## Load Balancing and Web Servers

Bộ cân bằng tải (Load balancers) cải thiện hiệu suất và độ tin cậy bằng cách phân phối khối lượng công việc trên nhiều máy chủ. Khi bộ cân bằng tải được tích hợp vào cơ sở hạ tầng của bạn, trước tiên một yêu cầu sẽ chuyển đến bộ cân bằng tải, điều này sẽ hướng nó đến máy chủ phụ trợ chính xác. Trong việc chọn một máy chủ, bộ cân bằng tải trước tiên sẽ kiểm tra máy chủ nào đang đáp ứng các yêu cầu một cách thích hợp. Sau đó, nó sử dụng một quy tắc được định cấu hình sẵn để chọn trong số các máy chủ khỏe mạnh.

## Giới thiệu về NGINX

NGINX là một máy chủ web hiệu suất cao được phát triển để hỗ trợ nhu cầu ngày càng cao của web hiện đại. Nó tập trung vào high performance, high concurrency và sử dụng tài nguyên thấp. Mặc dù nó chủ yếu được biết đến như một máy chủ web, nhưng cốt lõi của NGINX là một máy chủ proxy ngược (reverse proxy server).

Tuy nhiên, NGINX không phải là máy chủ web duy nhất trên thị trường. Một trong những đối thủ cạnh tranh lớn nhất của nó là Apache HTTP Server (httpd), được phát hành lần đầu tiên vào năm 1995. Mặc dù Apache HTTP Server linh hoạt hơn, quản trị viên máy chủ thường thích NGINX vì hai lý do chính:
- Nó có thể xử lý số lượng request đồng thời cao hơn.

- Nó có khả năng phân phối nội dung tĩnh nhanh hơn với mức sử dụng tài nguyên thấp.

Nginx xuất hiện sau Apache, với nhận thức rõ ràng hơn về các vấn đề đồng thời sẽ phải đối mặt với các trang web trên quy mô lớn. Tận dụng kiến ​​thức này, Nginx được thiết kế ngay từ đầu để sử dụng thuật toán xử lý kết nối theo hướng sự kiện, không đồng bộ, không chặn.

Nginx tạo ra các worker processes, mỗi processes có thể xử lý hàng nghìn kết nối. Các worker processes thực hiện điều này bằng cách triển khai cơ chế lặp nhanh liên tục kiểm tra và xử lý các sự kiện. Việc tách công việc thực tế khỏi các kết nối cho phép mỗi worker chỉ quan tâm đến kết nối khi một sự kiện mới đã được trigger.

![image](https://user-images.githubusercontent.com/51064915/183699197-2f6d1ff7-2bed-4054-9f97-4e0f6236c3f8.png)

NGINX nhanh hơn trong việc phân phối nội dung tĩnh và tương đối nhẹ về tài nguyên vì nó không nhúng bộ xử lý ngôn ngữ lập trình động. Khi có yêu cầu về nội dung tĩnh, NGINX chỉ cần trả lời bằng tệp mà không cần chạy bất kỳ quy trình bổ sung nào.

Điều đó không có nghĩa là NGINX không thể xử lý các request yêu cầu bộ xử lý ngôn ngữ lập trình động. Trong những trường hợp như vậy, NGINX chỉ cần ủy quyền các tác vụ cho các quy trình riêng biệt như PHP-FPM, Node.js hoặc Python. Sau đó, khi quá trình đó hoàn thành công việc của nó, NGINX sẽ ủy quyền ngược lại phản hồi cho máy khách.

![image](https://user-images.githubusercontent.com/51064915/183703051-b756664f-46cb-4edd-a578-df5d938e79aa.png)

NGINX cũng dễ dàng cấu hình hơn rất nhiều nhờ vào cú pháp tệp cấu hình lấy cảm hứng từ các ngôn ngữ kịch bản khác nhau, dẫn đến các tệp cấu hình nhỏ gọn, dễ bảo trì.

## Cài đặt NGINX

```
sudo apt-get update
sudo apt-get install nginx
```

Theo mặc định, Nginx được định cấu hình để khởi động tự động khi máy chủ khởi động. Nếu muốn, bạn có thể tắt hành vi này bằng cách nhập:
```
sudo systemctl disable nginx
```

Để bật lại dịch vụ khởi động khi khởi động, hãy nhập:
```
sudo systemctl enable nginx
```

Sau khi cài đặt xong, NGINX sẽ tự động được đăng ký như một dịch vụ systemd và sẽ active. Để kiểm tra, hãy thực hiện lệnh sau:
```
sudo systemctl status nginx

# ● nginx.service - A high performance web server and a reverse proxy server
#      Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
#      Active: active (running)
```

Nếu status là ative, thì bạn đã sẵn sàng. Nếu không, bạn có thể bắt đầu dịch vụ bằng cách thực hiện lệnh này:
```
sudo systemctl start nginx
```

Để dừng máy chủ Nginx đang chạy của bạn:
```
sudo systemctl stop nginx
```

Để có thể khởi động lại NGINX chạy:
```
sudo systemctl restart nginx
```

Bạn cũng có thể reload lại Nginx mà không làm gián đoạn kết nối:
```
sudo systemctl reload nginx
```

Theo mặc định, Nginx được định cấu hình để khởi động tự động khi máy chủ khởi động. Nếu muốn, bạn có thể bằng cách:
```
sudo systemctl disable nginx
```

Để bật lại service khởi động khi server được khởi động:
```
sudo systemctl enable nginx
```

NGINX thường được cài đặt trên thư mục /etc/nginx và phần lớn công việc của chúng ta trong các phần sắp tới sẽ được thực hiện tại đây.


## Tìm hiểu về config file NGINX

Là một máy chủ web, công việc của NGINX là cung cấp nội dung tĩnh hoặc động cho các máy khách. Nhưng nội dung đó sẽ được phân phát như thế nào thường được kiểm soát bởi các tệp cấu hình.

![image](https://user-images.githubusercontent.com/51064915/183942886-9b7c0da6-b40b-4254-9170-4002bcc94245.png)

Trong số các tệp trên, có một tệp có tên là nginx.conf . Đây là tệp cấu hình chính cho NGINX. Bạn có thể xem nội dung của tệp này:

```
$ cat nginx.conf

user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
	worker_connections 768;
	# multi_accept on;
}

http {

	##
	# Basic Settings
	##

	sendfile on;
	tcp_nopush on;
	tcp_nodelay on;
	keepalive_timeout 65;
	types_hash_max_size 2048;
	# server_tokens off;

	# server_names_hash_bucket_size 64;
	# server_name_in_redirect off;
    include /etc/nginx/mime.types;
	default_type application/octet-stream;

	##
	# SSL Settings
	##

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;

	##
	# Logging Settings
	##

	access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;

	##
	# Gzip Settings
	##

	gzip on;
    # gzip_vary on;
	# gzip_proxied any;
	# gzip_comp_level 6;
	# gzip_buffers 16 8k;
	# gzip_http_version 1.1;
	# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

	##
	# Virtual Host Configs
	##

	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;
}


#mail {
#	# See sample authentication script at:
#	# http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
# 
#	# auth_http localhost/auth.php;
#	# pop3_capabilities "TOP" "USER";
#	# imap_capabilities "IMAP4rev1" "UIDPLUS";
# 
#	server {
#		listen     localhost:110;
#		protocol   pop3;
#		proxy      on;
#	}
server {
#		listen     localhost:143;
#		protocol   imap;
#		proxy      on;
#	}
```

Có thể thấy nội dung trong file ban đầu rất nhiều và để có thể hiểu được chi tiết chúng ta cần chuyển nội dung trang sang một file khác và thực hiện tìm hiểu để biết được hết nội dung file

```
$ sudo mv nginx.conf nginx.conf.back
$ sudo touch nginx.conf
```



