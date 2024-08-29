# SSC-Internship-Dev-CSharpAdvanced-Async-Await
# Hướng Dẫn về Async và Await trong C#
## 1. Giới thiệu về Async và Await
Trong lập trình C#, `async` và `await` là hai từ khóa giúp lập trình viên xử lý các tác vụ không đồng bộ một cách dễ dàng và hiệu quả hơn. Bằng cách sử dụng chúng, bạn có thể thực thi mã không đồng bộ mà không chặn thread chính, giúp ứng dụng của bạn hoạt động mượt mà và phản hồi nhanh hơn.

## 2. Tại sao cần Async và Await?
Trong các ứng dụng, đặc biệt là các ứng dụng giao diện người dùng (UI), việc chờ đợi các tác vụ dài như tải dữ liệu từ internet, đọc/ghi file, hoặc kết nối với cơ sở dữ liệu có thể làm ứng dụng của bạn bị "đóng băng" nếu bạn thực thi chúng trên thread chính. Để tránh điều này, chúng ta cần sử dụng các tác vụ không đồng bộ, và `async/await` là công cụ lý tưởng cho việc này.

## 3. Sử dụng Async và Await trong C#
Khi bạn đánh dấu một phương thức với từ khóa `async`, phương thức đó sẽ chạy không đồng bộ và trả về một `Task` hoặc `Task<T>`. Trong phương thức này, bạn có thể sử dụng từ khóa `await` để chờ một tác vụ hoàn thành mà không chặn thread chính.

Cấu trúc cơ bản:

```csharp
public async Task<int> DoSomethingAsync()
{
    // Thực hiện một tác vụ không đồng bộ
    int result = await Task.Run(() => SomeLongRunningOperation());

    // Trả về kết quả
    return result;
}
```
## 4. Ví dụ cơ bản về Async và Await
Giả sử bạn có một tác vụ cần tải dữ liệu từ internet và hiển thị nó trên giao diện người dùng. Nếu thực hiện tác vụ này trên thread chính, giao diện người dùng sẽ bị "đóng băng" cho đến khi dữ liệu được tải xong. Dưới đây là cách sử dụng async và await để giải quyết vấn đề này:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

class Program
{
    static async Task Main(string[] args)
    {
        string url = "https://api.github.com/";
        string content = await FetchDataFromUrlAsync(url);
        Console.WriteLine(content);
    }

    static async Task<string> FetchDataFromUrlAsync(string url)
    {
        using (HttpClient client = new HttpClient())
        {
            client.DefaultRequestHeaders.UserAgent.ParseAdd("Mozilla/5.0 (compatible; GrandCircus/1.0)");
            HttpResponseMessage response = await client.GetAsync(url);
            return await response.Content.ReadAsStringAsync();
        }
    }
}
```
Giải thích:

- `FetchDataFromUrlAsync` là một phương thức không đồng bộ, nó sử dụng `HttpClient` để thực hiện một yêu cầu HTTP GET và trả về nội dung của phản hồi dưới dạng chuỗi.
- `await` đảm bảo rằng chương trình sẽ chờ cho đến khi nhiệm vụ hoàn thành trước khi tiếp tục.
## 5. Xử lý ngoại lệ trong Async và Await
Ngoại lệ trong các phương thức không đồng bộ có thể được xử lý bằng cách sử dụng khối `try-catch` giống như trong các phương thức đồng bộ.

```csharp
static async Task<string> FetchDataWithExceptionHandlingAsync(string url)
{
    try
    {
        using (HttpClient client = new HttpClient())
        {
            client.DefaultRequestHeaders.UserAgent.ParseAdd("Mozilla/5.0 (compatible; GrandCircus/1.0)");
            HttpResponseMessage response = await client.GetAsync(url);
            response.EnsureSuccessStatusCode();
            return await response.Content.ReadAsStringAsync();
        }
    }
    catch (HttpRequestException e)
    {
        Console.WriteLine($"Request error: {e.Message}");
        return string.Empty;
    }
}
```
Trong ví dụ trên, nếu có lỗi xảy ra trong quá trình gửi yêu cầu HTTP, ngoại lệ sẽ được bắt và xử lý trong khối `catch`.

## 6. Sử dụng Task và Task<T> với Async và Await
Các phương thức không đồng bộ thường trả về `Task` hoặc `Task<T>`. `Task` đại diện cho một tác vụ không trả về giá trị, trong khi `Task<T>` đại diện cho một tác vụ trả về giá trị kiểu `T`.

```csharp
static async Task<int> CalculateSumAsync(int a, int b)
{
    return await Task.Run(() => a + b);
}
```
Trong ví dụ này, `CalculateSumAsync` trả về một `Task<int>`, có nghĩa là kết quả của tác vụ này là một số nguyên.

## 7. Kết hợp Async/Await với các tác vụ không đồng bộ khác
Ngoài việc kết hợp với các phương thức không đồng bộ chuẩn, bạn cũng có thể sử dụng `async` và `await` với các tác vụ không đồng bộ tự định nghĩa hoặc các thư viện khác hỗ trợ không đồng bộ.

Ví dụ, bạn có thể sử dụng `async` và `await` để đọc và ghi tệp một cách không đồng bộ:

```csharp
using System;
using System.IO;
using System.Threading.Tasks;

class Program
{
    static async Task Main(string[] args)
    {
        string filePath = "example.txt";
        await WriteTextToFileAsync(filePath, "Hello, Async World!");
        string content = await ReadTextFromFileAsync(filePath);
        Console.WriteLine(content);
    }

    static async Task WriteTextToFileAsync(string filePath, string content)
    {
        using (StreamWriter writer = new StreamWriter(filePath))
        {
            await writer.WriteAsync(content);
        }
    }

    static async Task<string> ReadTextFromFileAsync(string filePath)
    {
        using (StreamReader reader = new StreamReader(filePath))
        {
            return await reader.ReadToEndAsync();
        }
    }
}
```
## 8. Tổng kết
Việc sử dụng `async` và `await` trong C# giúp bạn viết mã không đồng bộ một cách dễ dàng và hiệu quả hơn. Điều này không chỉ giúp tối ưu hóa hiệu suất của ứng dụng mà còn giúp mã của bạn trở nên rõ ràng và dễ bảo trì hơn. 
