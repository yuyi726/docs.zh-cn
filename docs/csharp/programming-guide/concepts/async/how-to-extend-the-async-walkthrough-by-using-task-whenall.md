---
title: 如何使用 Task.WhenAll 扩展异步演练 (C#)
ms.date: 07/20/2015
ms.assetid: f6927ef2-dc6c-43f8-bc82-bbeac42de423
ms.openlocfilehash: afd7dda4e876b7faa54ae4a8e62d640d2b9aaf07
ms.sourcegitcommit: 7588136e355e10cbc2582f389c90c127363c02a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "73970024"
---
# <a name="how-to-extend-the-async-walkthrough-by-using-taskwhenall-c"></a>如何使用 Task.WhenAll 扩展异步演练 (C#)

可以使用 [ 方法来改进](./walkthrough-accessing-the-web-by-using-async-and-await.md)演练：使用 Async 和 Await 访问 Web (C#)<xref:System.Threading.Tasks.Task.WhenAll%2A?displayProperty=nameWithType> 中的异步解决方案性能。 此方法以异步方式等待多个异步操作（它们表示为任务的集合）。

你可能已在演练中注意到网站以不同速率进行下载。 有时一个网站非常慢，这会延迟所有其余下载。 运行在演练中生成的异步解决方案时，如果不想等待，则可以方便地结束程序，但更好的选项是同时启动所有下载，并让较快的下载继续进行而不等待延迟的下载。

可将 `Task.WhenAll` 方法应用于任务的集合。 `WhenAll` 的应用程序返回单个任务，直到集合中的每个任务都已完成之后，该任务才会完成。 任务会表现为并行运行，但不会创建其他线程。 任务可以按任何顺序完成。

> [!IMPORTANT]
> 下面的过程介绍[演练：使用 Async 和 Await 访问 Web (C#)](./walkthrough-accessing-the-web-by-using-async-and-await.md) 中开发的异步应用程序的扩展。 可以通过完成演练或从[开发人员代码示例](https://code.msdn.microsoft.com/Async-Sample-Accessing-the-9c10497f)下载代码来开发应用程序。
>
> 若要运行示例，必须在计算机上安装 Visual Studio 2012 或更高版本。

### <a name="to-add-taskwhenall-to-your-geturlcontentsasync-solution"></a>向你的 GetURLContentsAsync 解决方案中添加 Task.WhenAll

1. 向`ProcessURLAsync`演练：使用 Async 和 Await 访问 Web (C#)[ 中开发的第一个应用程序添加 ](./walkthrough-accessing-the-web-by-using-async-and-await.md) 方法。

    - 如果是从[开发人员代码示例](https://code.msdn.microsoft.com/Async-Sample-Accessing-the-9c10497f)下载的代码，请打开 AsyncWalkthrough 项目，然后向 MainWindow.xaml.cs 文件添加 `ProcessURLAsync`。

    - 如果是通过完成演练开发的代码，请向包含 `ProcessURLAsync` 方法的应用程序添加 `GetURLContentsAsync`。 此应用程序的 MainWindow.xaml.cs 文件是“完成演练中的代码示例”部分中的第一个示例。

    `ProcessURLAsync` 方法整合了原始演练的 `foreach` 中的 `SumPageSizesAsync` 循环体中的操作。 该方法以异步方式将指定网站的内容作为字节数组进行下载，然后显示并返回字节数组的长度。

    ```csharp
    private async Task<int> ProcessURLAsync(string url)
    {
        var byteArray = await GetURLContentsAsync(url);
        DisplayResults(url, byteArray);
        return byteArray.Length;
    }
    ```

2. 注释禁止或删除 `foreach` 中的 `SumPageSizesAsync` 循环，如以下代码所示。

    ```csharp
    //var total = 0;
    //foreach (var url in urlList)
    //{
    //    byte[] urlContents = await GetURLContentsAsync(url);

    //    // The previous line abbreviates the following two assignment statements.
    //    // GetURLContentsAsync returns a Task<T>. At completion, the task
    //    // produces a byte array.
    //    //Task<byte[]> getContentsTask = GetURLContentsAsync(url);
    //    //byte[] urlContents = await getContentsTask;

    //    DisplayResults(url, urlContents);

    //    // Update the total.
    //    total += urlContents.Length;
    //}
    ```

3. 创建任务集合。 以下代码定义一个[查询](../linq/index.md)，由 <xref:System.Linq.Enumerable.ToArray%2A> 方法执行该查询时，它会创建下载每个网站内容的任务集合。 计算该查询时，会启动任务。

    在 `SumPageSizesAsync` 的声明后，将以下代码添加到方法 `urlList` 中。

    ```csharp
    // Create a query.
    IEnumerable<Task<int>> downloadTasksQuery =
        from url in urlList select ProcessURLAsync(url);

    // Use ToArray to execute the query and start the download tasks.
    Task<int>[] downloadTasks = downloadTasksQuery.ToArray();
    ```

4. 将 `Task.WhenAll` 应用于任务集合 `downloadTasks`。 `Task.WhenAll` 返回单个任务，它在任务集合中的所有任务都已完成时完成。

    在以下示例中，`await` 表达式等待 `WhenAll` 返回的单个任务完成。 该表达式的计算结果为整数数组，其中每个整数都是一个下载的网站的长度。 就在上一步添加的代码后，将以下代码添加到 `SumPageSizesAsync` 中。

    ```csharp
    // Await the completion of all the running tasks.
    int[] lengths = await Task.WhenAll(downloadTasks);

    //// The previous line is equivalent to the following two statements.
    //Task<int[]> whenAllTask = Task.WhenAll(downloadTasks);
    //int[] lengths = await whenAllTask;
    ```

5. 最后，使用 <xref:System.Linq.Enumerable.Sum%2A> 方法计算所有网站的长度总和。 将以下行添加到 `SumPageSizesAsync` 中。

    ```csharp
    int total = lengths.Sum();
    ```

### <a name="to-add-taskwhenall-to-the-httpclientgetbytearrayasync-solution"></a>向 HttpClient.GetByteArrayAsync 解决方案中添加 Task.WhenAll

1. 向`ProcessURLAsync`演练：使用 Async 和 Await 访问 Web (C#)[ 中开发的第二个应用程序添加以下版本的 ](./walkthrough-accessing-the-web-by-using-async-and-await.md)。

    - 如果是从[开发人员代码示例](https://code.msdn.microsoft.com/Async-Sample-Accessing-the-9c10497f)下载的代码，请打开 AsyncWalkthrough_HttpClient 项目，然后向 MainWindow.xaml.cs 文件添加 `ProcessURLAsync`。

    - 如果是通过完成演练开发的代码，请向使用 `ProcessURLAsync` 方法的应用程序添加 `HttpClient.GetByteArrayAsync`。 此应用程序的 MainWindow.xaml.cs 文件是“完成演练中的代码示例”部分中的第二个示例。

    `ProcessURLAsync` 方法整合了原始演练的 `foreach` 中的 `SumPageSizesAsync` 循环体中的操作。 该方法以异步方式将指定网站的内容作为字节数组进行下载，然后显示并返回字节数组的长度。

    与上面过程中的 `ProcessURLAsync` 方法的唯一区别是使用 <xref:System.Net.Http.HttpClient> 实例 `client`。

    ```csharp
    async Task<int> ProcessURLAsync(string url, HttpClient client)
    {
        byte[] byteArray = await client.GetByteArrayAsync(url);
        DisplayResults(url, byteArray);
        return byteArray.Length;
    }
    ```

2. 注释禁止或删除 `For Each` 中的 `foreach` 或 `SumPageSizesAsync` 循环，如以下代码所示。

    ```csharp
    //var total = 0;
    //foreach (var url in urlList)
    //{
    //    // GetByteArrayAsync returns a Task<T>. At completion, the task
    //    // produces a byte array.
    //    byte[] urlContent = await client.GetByteArrayAsync(url);

    //    // The previous line abbreviates the following two assignment
    //    // statements.
    //    Task<byte[]> getContentTask = client.GetByteArrayAsync(url);
    //    byte[] urlContent = await getContentTask;

    //    DisplayResults(url, urlContent);

    //    // Update the total.
    //    total += urlContent.Length;
    //}
    ```

3. 定义一个[查询](../linq/index.md)，由 <xref:System.Linq.Enumerable.ToArray%2A> 方法执行该查询时，它会创建下载每个网站内容的任务集合。 计算该查询时，会启动任务。

    在 `SumPageSizesAsync` 和 `client` 的声明后，将以下代码添加到方法 `urlList` 中。

    ```csharp
    // Create a query.
    IEnumerable<Task<int>> downloadTasksQuery =
        from url in urlList select ProcessURLAsync(url, client);

    // Use ToArray to execute the query and start the download tasks.
    Task<int>[] downloadTasks = downloadTasksQuery.ToArray();
    ```

4. 接下来，将 `Task.WhenAll` 应用于任务集合 `downloadTasks`。 `Task.WhenAll` 返回单个任务，它在任务集合中的所有任务都已完成时完成。

    在以下示例中，`await` 表达式等待 `WhenAll` 返回的单个任务完成。 完成后，`await` 表达式的计算结果为整数数组，其中每个整数都是一个下载的网站的长度。 就在上一步添加的代码后，将以下代码添加到 `SumPageSizesAsync` 中。

    ```csharp
    // Await the completion of all the running tasks.
    int[] lengths = await Task.WhenAll(downloadTasks);

    //// The previous line is equivalent to the following two statements.
    //Task<int[]> whenAllTask = Task.WhenAll(downloadTasks);
    //int[] lengths = await whenAllTask;
    ```

5. 最后，使用 <xref:System.Linq.Enumerable.Sum%2A> 方法获取所有网站的长度总和。 将以下行添加到 `SumPageSizesAsync` 中。

    ```csharp
    int total = lengths.Sum();
    ```

### <a name="to-test-the-taskwhenall-solutions"></a>测试 Task.WhenAll 解决方案

- 对于任一解决方案，按 F5 键以运行程序，然后选择“启动”  按钮。 输出应类似于[演练：使用 Async 和 Await 访问 Web (C#)](./walkthrough-accessing-the-web-by-using-async-and-await.md) 中的异步解决方案的输出。 但请注意，网站每次会以不同顺序出现。

## <a name="example"></a>示例

以下代码演示使用 `GetURLContentsAsync` 方法从 Web 下载内容的项目的扩展。

```csharp
// Add the following using directives, and add a reference for System.Net.Http.
using System.Net.Http;
using System.IO;
using System.Net;

namespace AsyncExampleWPF_WhenAll
{
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }

        private async void startButton_Click(object sender, RoutedEventArgs e)
        {
            resultsTextBox.Clear();

            // Two-step async call.
            Task sumTask = SumPageSizesAsync();
            await sumTask;

            // One-step async call.
            //await SumPageSizesAsync();

            resultsTextBox.Text += "\r\nControl returned to startButton_Click.\r\n";
        }

        private async Task SumPageSizesAsync()
        {
            // Make a list of web addresses.
            List<string> urlList = SetUpURLList();

            // Create a query.
            IEnumerable<Task<int>> downloadTasksQuery =
                from url in urlList select ProcessURLAsync(url);

            // Use ToArray to execute the query and start the download tasks.
            Task<int>[] downloadTasks = downloadTasksQuery.ToArray();

            // You can do other work here before awaiting.

            // Await the completion of all the running tasks.
            int[] lengths = await Task.WhenAll(downloadTasks);

            //// The previous line is equivalent to the following two statements.
            //Task<int[]> whenAllTask = Task.WhenAll(downloadTasks);
            //int[] lengths = await whenAllTask;

            int total = lengths.Sum();

            //var total = 0;
            //foreach (var url in urlList)
            //{
            //    byte[] urlContents = await GetURLContentsAsync(url);

            //    // The previous line abbreviates the following two assignment statements.
            //    // GetURLContentsAsync returns a Task<T>. At completion, the task
            //    // produces a byte array.
            //    //Task<byte[]> getContentsTask = GetURLContentsAsync(url);
            //    //byte[] urlContents = await getContentsTask;

            //    DisplayResults(url, urlContents);

            //    // Update the total.
            //    total += urlContents.Length;
            //}

            // Display the total count for all of the websites.
            resultsTextBox.Text +=
                $"\r\n\r\nTotal bytes returned:  {total}\r\n";
        }

        private List<string> SetUpURLList()
        {
            List<string> urls = new List<string>
            {
                "https://msdn.microsoft.com",
                "https://msdn.microsoft.com/library/windows/apps/br211380.aspx",
                "https://msdn.microsoft.com/library/hh290136.aspx",
                "https://msdn.microsoft.com/library/ee256749.aspx",
                "https://msdn.microsoft.com/library/hh290138.aspx",
                "https://msdn.microsoft.com/library/hh290140.aspx",
                "https://msdn.microsoft.com/library/dd470362.aspx",
                "https://msdn.microsoft.com/library/aa578028.aspx",
                "https://msdn.microsoft.com/library/ms404677.aspx",
                "https://msdn.microsoft.com/library/ff730837.aspx"
            };
            return urls;
        }

        // The actions from the foreach loop are moved to this async method.
        private async Task<int> ProcessURLAsync(string url)
        {
            var byteArray = await GetURLContentsAsync(url);
            DisplayResults(url, byteArray);
            return byteArray.Length;
        }

        private async Task<byte[]> GetURLContentsAsync(string url)
        {
            // The downloaded resource ends up in the variable named content.
            var content = new MemoryStream();

            // Initialize an HttpWebRequest for the current URL.
            var webReq = (HttpWebRequest)WebRequest.Create(url);

            // Send the request to the Internet resource and wait for
            // the response.
            using (WebResponse response = await webReq.GetResponseAsync())
            {
                // Get the data stream that is associated with the specified url.
                using (Stream responseStream = response.GetResponseStream())
                {
                    await responseStream.CopyToAsync(content);
                }
            }

            // Return the result as a byte array.
            return content.ToArray();

        }

        private void DisplayResults(string url, byte[] content)
        {
            // Display the length of each website. The string format
            // is designed to be used with a monospaced font, such as
            // Lucida Console or Global Monospace.
            var bytes = content.Length;
            // Strip off the "https://".
            var displayURL = url.Replace("https://", "");
            resultsTextBox.Text += $"\n{displayURL,-58} {bytes,8}";
        }
    }
}
```

## <a name="example"></a>示例

以下代码演示使用 `HttpClient.GetByteArrayAsync` 方法从 Web 下载内容的项目的扩展。

```csharp
// Add the following using directives, and add a reference for System.Net.Http.
using System.Net.Http;
using System.IO;
using System.Net;

namespace AsyncExampleWPF_HttpClient_WhenAll
{
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }

        private async void startButton_Click(object sender, RoutedEventArgs e)
        {
            resultsTextBox.Clear();

            // One-step async call.
            await SumPageSizesAsync();

            // Two-step async call.
            //Task sumTask = SumPageSizesAsync();
            //await sumTask;

            resultsTextBox.Text += "\r\nControl returned to startButton_Click.\r\n";
        }

        private async Task SumPageSizesAsync()
        {
            // Make a list of web addresses.
            List<string> urlList = SetUpURLList();

            // Declare an HttpClient object and increase the buffer size. The
            // default buffer size is 65,536.
            HttpClient client = new HttpClient() { MaxResponseContentBufferSize = 1000000 };

            // Create a query.
            IEnumerable<Task<int>> downloadTasksQuery =
                from url in urlList select ProcessURLAsync(url, client);

            // Use ToArray to execute the query and start the download tasks.
            Task<int>[] downloadTasks = downloadTasksQuery.ToArray();

            // You can do other work here before awaiting.

            // Await the completion of all the running tasks.
            int[] lengths = await Task.WhenAll(downloadTasks);

            //// The previous line is equivalent to the following two statements.
            //Task<int[]> whenAllTask = Task.WhenAll(downloadTasks);
            //int[] lengths = await whenAllTask;

            int total = lengths.Sum();

            //var total = 0;
            //foreach (var url in urlList)
            //{
            //    // GetByteArrayAsync returns a Task<T>. At completion, the task
            //    // produces a byte array.
            //    byte[] urlContent = await client.GetByteArrayAsync(url);

            //    // The previous line abbreviates the following two assignment
            //    // statements.
            //    Task<byte[]> getContentTask = client.GetByteArrayAsync(url);
            //    byte[] urlContent = await getContentTask;

            //    DisplayResults(url, urlContent);

            //    // Update the total.
            //    total += urlContent.Length;
            //}

            // Display the total count for all of the web addresses.
            resultsTextBox.Text +=
                $"\r\n\r\nTotal bytes returned:  {total}\r\n";
        }

        private List<string> SetUpURLList()
        {
            List<string> urls = new List<string>
            {
                "https://msdn.microsoft.com",
                "https://msdn.microsoft.com/library/hh290136.aspx",
                "https://msdn.microsoft.com/library/ee256749.aspx",
                "https://msdn.microsoft.com/library/hh290138.aspx",
                "https://msdn.microsoft.com/library/hh290140.aspx",
                "https://msdn.microsoft.com/library/dd470362.aspx",
                "https://msdn.microsoft.com/library/aa578028.aspx",
                "https://msdn.microsoft.com/library/ms404677.aspx",
                "https://msdn.microsoft.com/library/ff730837.aspx"
            };
            return urls;
        }

        // The actions from the foreach loop are moved to this async method.
        async Task<int> ProcessURLAsync(string url, HttpClient client)
        {
            byte[] byteArray = await client.GetByteArrayAsync(url);
            DisplayResults(url, byteArray);
            return byteArray.Length;
        }

        private void DisplayResults(string url, byte[] content)
        {
            // Display the length of each web site. The string format
            // is designed to be used with a monospaced font, such as
            // Lucida Console or Global Monospace.
            var bytes = content.Length;
            // Strip off the "https://".
            var displayURL = url.Replace("https://", "");
            resultsTextBox.Text += $"\n{displayURL,-58} {bytes,8}";
        }
    }
}
```

## <a name="see-also"></a>另请参阅

- <xref:System.Threading.Tasks.Task.WhenAll%2A?displayProperty=nameWithType>
- [演练：使用 Async 和 Await 访问 Web (C#)](./walkthrough-accessing-the-web-by-using-async-and-await.md)
