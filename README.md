# Middleware là gì? Tìm hiểu về Middleware và tự tạo một middleware tùy chỉnh

## Middleware là gì?

Middleware là những đoạn mã trung gian nằm giữa các request và response. Nó nhận các request, thi hành các mệnh lệnh tương ứng trên request đó. Sau khi hoàn thành nó response (trả về) hoặc chuyển kết quả ủy thác cho một Middleware khác trong hàng đợi.

![image](https://github.com/thangdtph27626/Middleware/assets/109157942/6c2c0cff-d670-41fc-b525-e1cd7c63b320)

## Ứng dụng Middleware là gì?

Hiện nay các Web Framework tân tiến đều sử dụng nó như là một phần của ứng dụng để kết nối các phần khác lại với nhau. Đối với các ứng dụng web, việc sử dụng Middleware một cách hiệu quả giúp chúng ta có thể tối giản được số lượng dòng code phải viết trong ứng dụng.

Một ví dụ phổ biến mà chúng ta thường phải dùng Middleware đó là các trang chỉ dành riêng cho admin và không cho phép người dùng bình thường có thể truy cập.

## Tại sao nên sử dụng nó?

Với tư tưởng chung là cầu nối giữa tương tác của người dùng và hệ thống trong lập trình Web. Middleware sẽ đóng vai trò trung gian giữa request/response và các xử lý logic bên trong web server.

Do đó, Middleware trong các Framework cho ứng dụng Web (Laravel, Django, Rails, ExpressJS…), sẽ là các hàm được dùng để tiền xử lý, lọc các request trước khi đưa vào xử lý logic hoặc điều chỉnh các response trước khi gửi về cho người dùng.

## Hiểu các khái niệm cơ bản của Laravel Middleware

Trong bài viết này, mình sẽ lấy ví dụ là dùng framework Laravel để hiểu khái niệm về middleware. Chúng ta sẽ xem xét cách tạo middleware tùy chỉnh trong một ứng dụng Laravel.

Sau khi tạo middleware tùy chỉnh của bạn, chúng ta sẽ khám phá các tùy chọn có sẵn để đăng ký nó với Laravel để nó có thể thực sự được gọi trong luồng xử lý yêu cầu.

## Middleware trong Laravel là gì?

Middleware như  là một cơ chế cho phép bạn tham gia vào luồng xử lý request của một ứng dụng Laravel. Trong một quá trình xử lý route điển hình của Laravel khi thực thi việc xử lý yêu cầu và middleware là một trong những class mà ứng dụng phải thông qua.

Vậy chính xác thì việc xử lý luồng yêu cầu Laravel là gì? Ví dụ: cần xác thực người dùng để quyết định xem họ có được phép truy cập đến route hiện tại hay không.

- Yêu cầu đăng nhập
- Chuyển hướng người dùng
- Thay đổi/chuẩn hoá các tham số
- Xử lý response được ứng dụng Laravel tạo ra
- …
Thực tế, Laravel mặc định đã có sẵn một số middleware quan trọng. Việc xác thực người dùng cũng được chính middleware này thực thi.

## Tạo một middleware tùy chỉnh

Tại đây, bạn sẽ tìm hiểu cách tạo và thêm phần mềm trung gian tùy chỉnh của riêng mình vào đường dẫn yêu cầu của ứng dụng ASP.NET Core.

Thành phần phần mềm trung gian tùy chỉnh giống như bất kỳ lớp .NET nào khác có Invoke()phương thức. Tuy nhiên, để thực thi phần mềm trung gian tiếp theo theo trình tự, nó phải có RequestDelegatetham số kiểu trong hàm tạo.

Visual Studio bao gồm mẫu để tạo lớp phần mềm trung gian tiêu chuẩn. Để làm điều này, nhấp chuột phải vào dự án hoặc thư mục mà bạn muốn tạo lớp phần mềm trung gian và chọn Thêm -> Mục mới. Thao tác này sẽ mở cửa sổ bật lên Thêm mục mới. Tìm kiếm từ “middleware” ở ô tìm kiếm trên cùng bên phải như hình dưới đây.

![image](https://github.com/thangdtph27626/Middleware/assets/109157942/35cf1625-b0a4-4ea2-911e-392c219be333)

Chọn mục Middleware Class và đặt tên cho nó rồi nhấp vào nút Thêm. Điều này sẽ thêm một lớp mới cho phần mềm trung gian với phương thức mở rộng như bên dưới.

```

// You may need to install the Microsoft.AspNetCore.Http.Abstractions package into your project
public class MyMiddleware
{
    private readonly RequestDelegate _next;

    public MyMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public Task Invoke(HttpContext httpContext)
    {

        return _next(httpContext);
    }
}

// Extension method used to add the middleware to the HTTP request pipeline.
public static class MyMiddlewareExtensions
{
    public static IApplicationBuilder UseMyMiddleware(this IApplicationBuilder builder)
    {
        return builder.UseMiddleware<MyMiddleware>();
    }
} 
```

Như bạn có thể thấy ở trên, phương thức Invoke() không đồng bộ. Vì vậy, hãy thay đổi nó thành không đồng bộ và viết logic tùy chỉnh của bạn trước khi gọi next();

```
public class MyMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger _logger;

    public MyMiddleware(RequestDelegate next, ILoggerFactory logFactory)
    {
        _next = next;

        _logger = logFactory.CreateLogger("MyMiddleware");
    }

    public async Task Invoke(HttpContext httpContext)
    {
        _logger.LogInformation("MyMiddleware executing..");

        await _next(httpContext); // calling next middleware

    }
}

// Extension method used to add the middleware to the HTTP request pipeline.
public static class MyMiddlewareExtensions
{
    public static IApplicationBuilder UseMyMiddleware(this IApplicationBuilder builder)
    {
        return builder.UseMiddleware<MyMiddleware>();
    }
} 
```

Thêm phần mềm trung gian tùy chỉnh
Bây giờ, chúng ta cần thêm phần mềm trung gian tùy chỉnh của mình vào đường dẫn yêu cầu bằng cách sử dụng phương thức mở rộng Sử dụng như hiển thị bên dưới.

```
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseMyMiddleware();

    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```

Chúng ta cũng có thể thêm phần mềm trung gian bằng app.UseMiddleware<MyMiddleware>()phương pháp IApplicationBuilder.

Vì vậy, chúng ta có thể thêm phần mềm trung gian tùy chỉnh vào ứng dụng ASP.NET Core.
