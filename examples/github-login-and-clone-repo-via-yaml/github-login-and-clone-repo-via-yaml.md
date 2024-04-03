###đăng nhập github để sao chép tập tin repo và dòng lệnh yaml? Làm sao ?
Để sao chép kho lưu trữ GitHub riêng tư trong setupcác lệnh của tác vụ, bạn cần thiết lập chuyển tiếp tác nhân SSH. Đây là cách bạn có thể làm điều đó:
Chạy các lệnh sau trên máy cục bộ của bạn:
```bash
eval $(ssh-agent -s)
ssh-add ~/.ssh/id_rsa
```

Sau đó, bất kỳ cụm SkyPilot nào được khởi chạy từ máy này sẽ có thể sao chép các kho lưu trữ GitHub riêng tư. Ví dụ: trong tệp YAML của bạn:

```yaml
setup: |
 git clone git@github.com:your-proj/your-repo.git
``` 
Xin lưu ý rằng hiện tại, việc sao chép kho lưu trữ riêng tư trong các runlệnh chưa được hỗ trợ.
Để biết thêm chi tiết, bạn có thể tham khảo tài liệu SkyPilot .