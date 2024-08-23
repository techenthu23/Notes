

### Python 3.x
```bash
python3 -m http.server 8080
```

### Python 2.x
```bash
python -m SimpleHTTPServer 8080
```

In addition to the basic HTTP server options in Python, there are several other ways to start an HTTP server with more features and flexibility:

### Using `http.server` Module (Python 3.x)
You can create a custom HTTP server by subclassing `BaseHTTPRequestHandler` and using `HTTPServer` or `ThreadingHTTPServer` for handling requests. This allows you to define custom behavior for different HTTP methods (e.g., GET, POST).

### Using Flask
Flask is a lightweight web framework that makes it easy to create web applications and APIs. Here's a simple example:
```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello, World!"

if __name__ == '__main__':
    app.run(port=8080)
```

### Using FastAPI
FastAPI is a modern, fast (high-performance) web framework for building APIs with Python 3.7+ based on standard Python type hints.
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"Hello": "World"}

if __name__ == '__main__':
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8080)
```

### Using Django
Django is a high-level Python web framework that encourages rapid development and clean, pragmatic design. Here's a basic example:
```bash
django-admin startproject mysite
cd mysite
python manage.py runserver 8080
```

### Using `http.server` with HTTPS
You can also start an HTTPS server using the `http.server` module with SSL support:
```python
import http.server
import ssl

server_address = ('', 4443)
httpd = http.server.HTTPServer(server_address, http.server.SimpleHTTPRequestHandler)
httpd.socket = ssl.wrap_socket(httpd.socket, certfile='path/to/cert.pem', server_side=True)
httpd.serve_forever()
```

These options provide different levels of functionality and complexity, depending on your needs¹²³.

Would you like more details on any of these options or help with something else?

- **References**:
- [http.server — HTTP servers — Python 3.12.5 documentation](https://docs.python.org/3/library/http.server.html)
- [How to Launch an HTTP Server in One Line of Python Code](https://realpython.com/python-http-server/)
- [Using Python HttpServer as a simple HTTP Server - AskPython](https://www.askpython.com/python-modules/python-httpserver)
- [Python Simple HTTP Server: A Quick and Easy Way to Serve Web Content](https://programmingdigest.com/python-simple-http-server-a-quick-and-easy-way-to-serve-web-content/)
- [Python HTTP(S) Server — Example · AnvilEight Blog](https://anvileight.com/blog/posts/simple-python-http-server/)
- [9 Python Web Servers to Try for Your Next Project - Geekflare](https://geekflare.com/python-web-servers/)



Sure! Here are some quick command-line HTTP server options in Python, Ruby, and Java that allow for file uploads and downloads:

### Python
You can use the `uploadserver` module, which extends the basic HTTP server to support file uploads:
1. **Install the module:**
    ```bash
    python3 -m pip install --user uploadserver
    ```
2. **Start the server:**
    ```bash
    python3 -m uploadserver 8080
    ```
   This will start an HTTP server on port 8080, with an upload page available at `/upload`³.

### Ruby
Ruby has a simple HTTP server that can be extended to handle file uploads. Here's a basic example using the `webrick` library:
1. **Install the `webrick` gem (if not already installed):**
    ```bash
    gem install webrick
    ```
2. **Create a server script (e.g., `server.rb`):**
    ```ruby
    require 'webrick'

    server = WEBrick::HTTPServer.new :Port => 8080, :DocumentRoot => Dir.pwd

    server.mount_proc '/upload' do |req, res|
      if req.request_method == 'POST'
        file = req.query['file']
        File.open(File.join(Dir.pwd, file.original_filename), 'wb') do |f|
          f.write(file.read)
        end
        res.body = 'File uploaded successfully'
      else
        res.body = <<-HTML
          <html><body>
          <form method="post" enctype="multipart/form-data">
            <input type="file" name="file">
            <input type="submit" value="Upload">
          </form>
          </body></html>
        HTML
      end
    end

    trap 'INT' do server.shutdown end
    server.start
    ```
3. **Run the server:**
    ```bash
    ruby server.rb
    ```

### Java
For Java, you can use the `SimpleHTTPServer` library. Here's a basic example:
1. **Create a server script (e.g., `SimpleHTTPServer.java`):**
    ```java
    import com.sun.net.httpserver.HttpServer;
    import com.sun.net.httpserver.HttpHandler;
    import com.sun.net.httpserver.HttpExchange;
    import java.io.*;
    import java.net.InetSocketAddress;

    public class SimpleHTTPServer {
        public static void main(String[] args) throws Exception {
            HttpServer server = HttpServer.create(new InetSocketAddress(8080), 0);
            server.createContext("/upload", new UploadHandler());
            server.setExecutor(null);
            server.start();
        }

        static class UploadHandler implements HttpHandler {
            @Override
            public void handle(HttpExchange exchange) throws IOException {
                if ("POST".equals(exchange.getRequestMethod())) {
                    InputStream is = exchange.getRequestBody();
                    File file = new File("uploaded_file");
                    FileOutputStream fos = new FileOutputStream(file);
                    byte[] buffer = new byte[1024];
                    int bytesRead;
                    while ((bytesRead = is.read(buffer)) != -1) {
                        fos.write(buffer, 0, bytesRead);
                    }
                    fos.close();
                    String response = "File uploaded successfully";
                    exchange.sendResponseHeaders(200, response.length());
                    OutputStream os = exchange.getResponseBody();
                    os.write(response.getBytes());
                    os.close();
                } else {
                    String response = "<html><body><form method='post' enctype='multipart/form-data'><input type='file' name='file'><input type='submit' value='Upload'></form></body></html>";
                    exchange.sendResponseHeaders(200, response.length());
                    OutputStream os = exchange.getResponseBody();
                    os.write(response.getBytes());
                    os.close();
                }
            }
        }
    }
    ```
2. **Compile and run the server:**
    ```bash
    javac SimpleHTTPServer.java
    java SimpleHTTPServer
    ```

These scripts will start HTTP servers on port 8080, allowing you to upload and download files.

Would you like more details on any of these options or help with something else?

- **References**:
- [uploadserver · PyPI](https://pypi.org/project/uploadserver/)
- [How to Launch an HTTP Server in One Line of Python Code](https://realpython.com/python-http-server/)
- [Serving Files with Python's SimpleHTTPServer Module - Stack Abuse](https://stackabuse.com/serving-files-with-pythons-simplehttpserver-module/)
- [How To Send Files Using Python Built-In Http Server](https://www.geeksforgeeks.org/how-to-send-files-using-python-built-in-http-server/)
- [Python's SimpleHTTPServer: A Quick Guide to Local Web Hosting](https://www.paulserban.eu/blog/post/pythons-simplehttpserver-a-quick-guide-to-local-web-hosting/)
- [http.server — HTTP servers — Python 3.12.5 documentation](https://docs.python.org/3/library/http.server.html)
- [Using Python HttpServer as a simple HTTP Server - AskPython](https://www.askpython.com/python-modules/python-httpserver)
- [Python Simple HTTP Server: A Quick and Easy Way to Serve Web Content](https://programmingdigest.com/python-simple-http-server-a-quick-and-easy-way-to-serve-web-content/)
- [Python HTTP(S) Server — Example · AnvilEight Blog](https://anvileight.com/blog/posts/simple-python-http-server/)
- [9 Python Web Servers to Try for Your Next Project - Geekflare](https://geekflare.com/python-web-servers/)
- [github.com](https://github.com/SelectiveBamboo/SecureDropbox/tree/9fca96561cf245b9db209d14c4d76dfb45b1c0f3/src%2FSimpleCloud.java)



Certainly! You can start a simple HTTP server in Ruby with a one-liner using the `WEBrick` library. Here's how you can do it:

```bash
ruby -rwebrick -e 'WEBrick::HTTPServer.new(:Port => 8000, :DocumentRoot => Dir.pwd).start'
```

This command will start an HTTP server on port 8000, serving the contents of the current directory¹².

Would you like to know more about using Ruby for web development or any other topic?

- **References**:
- [Big list of http static server one-liners · GitHub](https://gist.github.com/willurd/5720255)
- [A simple one-line webserver in Ruby | thoughtful](https://thoughtfulapps.com/articles/ruby/one-line-ruby-webserver)
- [GitHub - alphabet/simplest_ruby_http_server: A bone simple 3-line HTTP ...](https://github.com/alphabet/simplest_ruby_http_server)
- [Ruby web server one-liner (Example) - Coderwall](https://coderwall.com/p/fff9yw/ruby-web-server-one-liner)
- [undefined](https://gist.github.com/willurd/5720255.js&quot)


In addition to the one-liner using `WEBrick`, there are several other options for starting an HTTP server in Ruby, each with its own features and use cases:

### Using `Rack`
Rack provides a minimal interface between web servers and Ruby web applications. Here's a simple example:
```ruby
# Install the rack gem if you haven't already
gem install rack

# Create a config.ru file
run lambda { |env| [200, { "Content-Type" => "text/html" }, ["Hello, World!"]] }

# Start the server
rackup -p 8080
```

### Using `Sinatra`
Sinatra is a DSL for quickly creating web applications in Ruby with minimal effort:
```ruby
# Install the sinatra gem if you haven't already
gem install sinatra

# Create a server script (e.g., app.rb)
require 'sinatra'

get '/' do
  'Hello, World!'
end

# Start the server
ruby app.rb -p 8080
```

### Using `Puma`
Puma is a fast, multi-threaded HTTP server for Ruby/Rack applications:
```ruby
# Install the puma gem if you haven't already
gem install puma

# Create a config.ru file
run lambda { |env| [200, { "Content-Type" => "text/html" }, ["Hello, World!"]] }

# Start the server
puma -p 8080
```

### Using `Rails`
Rails is a full-stack web application framework. Here's how to start a simple server:
```bash
# Install Rails if you haven't already
gem install rails

# Create a new Rails application
rails new myapp
cd myapp

# Start the server
rails server -p 8080
```

### Using `Net::HTTP`
For more control over HTTP requests and responses, you can use the `Net::HTTP` library:
```ruby
require 'net/http'
require 'uri'

uri = URI.parse("http://localhost:8080")
response = Net::HTTP.get_response(uri)
puts response.body
```

These options provide different levels of functionality and complexity, depending on your needs¹²³.

Would you like more details on any of these options or help with something else?

- **References**:
- [class Net::HTTP - Documentation for Ruby 3.4 - ruby-lang.org](https://docs.ruby-lang.org/en/master/Net/HTTP.html)
- [The Ultimate Guide to HTTP Requests in Ruby - RubyGuides](https://www.rubyguides.com/2018/08/ruby-http-request/)
- [HTTP Server in Ruby from Scratch (Example) | GoRails](https://gorails.com/episodes/ruby-http-server-from-scratch)
- [class Net::HTTP - net: Ruby Standard Library Documentation](https://ruby-doc.org/3.2.2/stdlibs/net/Net/HTTP.html)
- [undefined](https://jsonplaceholder.typicode.com/)
       



Here are some useful one-liner Python commands for various tasks:

### Basic Operations
- **Print a string:**
  ```bash
  python -c "print('Hello, World!')"
  ```

- **Calculate the sum of a list:**
  ```bash
  python -c "print(sum([1, 2, 3, 4, 5]))"
  ```

### File Operations
- **Read a file and print its contents:**
  ```bash
  python -c "print(open('filename.txt').read())"
  ```

- **Count the number of lines in a file:**
  ```bash
  python -c "print(len(open('filename.txt').readlines()))"
  ```

### List Comprehensions
- **Create a list of squares:**
  ```bash
  python -c "print([x**2 for x in range(10)])"
  ```

- **Filter even numbers from a list:**
  ```bash
  python -c "print([x for x in range(10) if x % 2 == 0])"
  ```

### Lambda Functions
- **Sort a list of tuples by the second element:**
  ```bash
  python -c "print(sorted([(1, 3), (2, 2), (3, 1)], key=lambda x: x[1]))"
  ```

### HTTP Server
- **Start a simple HTTP server:**
  ```bash
  python -m http.server 8000
  ```

### Miscellaneous
- **Swap two variables:**
  ```bash
  python -c "a, b = 1, 2; a, b = b, a; print(a, b)"
  ```

- **Generate a list of Fibonacci numbers:**
  ```bash
  python -c "fib = lambda x: x if x <= 1 else fib(x-1) + fib(x-2); print([fib(x) for x in range(10)])"
  ```


- **References**:
- [Powerful Python One-Liners - Python Wiki](https://wiki.python.org/moin/Powerful%20Python%20One-Liners)
- [Powerful One-Liner Python codes - GeeksforGeeks](https://www.geeksforgeeks.org/powerful-one-liner-python-codes/)
- [Python Programming/Command-line one-liners - Wikibooks](https://en.wikibooks.org/wiki/Python_Programming/Command-line_one-liners)
- [Python One-Liners to Help You Write Simple, Readable Code](https://www.freecodecamp.org/news/python-one-liners/)
- [Getty Images](https://www.gettyimages.com/detail/news-photo/in-this-photo-illustration-a-python-logo-seen-displayed-on-news-photo/1986209604)


Here are some useful one-liners for data manipulation in Python, leveraging libraries like `pandas` and `numpy`:

### Using `pandas`
- **Read a CSV file into a DataFrame:**
  ```python
  import pandas as pd; df = pd.read_csv('file.csv')
  ```

- **Display the first 5 rows of a DataFrame:**
  ```python
  print(df.head())
  ```

- **Filter rows based on a condition:**
  ```python
  filtered_df = df[df['column_name'] > 10]
  ```

- **Select specific columns:**
  ```python
  selected_columns = df[['column1', 'column2']]
  ```

- **Group by and aggregate:**
  ```python
  grouped = df.groupby('column_name').sum()
  ```

- **Handle missing values by filling with a specific value:**
  ```python
  df.fillna(0, inplace=True)
  ```

### Using `numpy`
- **Create an array:**
  ```python
  import numpy as np; arr = np.array([1, 2, 3, 4, 5])
  ```

- **Calculate the mean of an array:**
  ```python
  mean = np.mean(arr)
  ```

- **Element-wise addition of two arrays:**
  ```python
  arr1 = np.array([1, 2, 3]); arr2 = np.array([4, 5, 6]); result = arr1 + arr2
  ```

- **Generate an array of random numbers:**
  ```python
  random_numbers = np.random.rand(10)
  ```

- **Reshape an array:**
  ```python
  reshaped = arr.reshape((5, 1))
  ```

### General Python
- **List comprehension to create a list of squares:**
  ```python
  squares = [x**2 for x in range(10)]
  ```

- **Flatten a list of lists:**
  ```python
  flat_list = [item for sublist in [[1, 2], [3, 4], [5, 6]] for item in sublist]
  ```

- **Sort a list of dictionaries by a key:**
  ```python
  sorted_list = sorted(list_of_dicts, key=lambda x: x['key_name'])
  ```

These one-liners can help you perform common data manipulation tasks quickly and efficiently¹²³.

Would you like more examples or details on any specific data manipulation task?

- **References**:
- [Efficient Data Manipulation in Python Using Pandas: Tips and Tricks](https://www.datarodeo.io/python/efficient-data-manipulation-in-python-using-pandas-tips-and-tricks/)
- [15 Most Powerful Python One-liners You Can’t Skip](https://www.codeforests.com/2021/02/01/most-powerful-python-one-liners/)
- [12 Useful Python One-Liners You Must Know](https://www.makeuseof.com/useful-python-one-liners-you-must-know/)
- [16 Useful Python One-Liners to Simplify Common Tasks](https://geekflare.com/useful-python-one-liners/)
- [Python One-Liners [Tutorial Collection] - Finxter](https://blog.finxter.com/python-one-liners-tutorial-collection/)
- [Essential Python Libraries for Data Manipulation - KDnuggets](https://www.kdnuggets.com/essential-python-libraries-for-data-manipulation)
- [15 Most Powerful Python One-liners You Can’t Skip](https://bing.com/search?q=Python+one-liners+for+data+manipulation)