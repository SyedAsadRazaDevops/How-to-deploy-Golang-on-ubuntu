# How to deploy Golang application on any GNU/Linux system, without Docker?
Hey, hey! 👋 Another interesting article about deploy and Golang is here.
Let's imagine that we need to run an application written on Go on a remote server, but configure Docker too long

# How to install the current version of Go in Ubuntu
For fetching the latest version:
```
sudo add-apt-repository ppa:longsleep/golang-backports
sudo apt update
sudo apt install golang-go
```
[https://stackoverflow.com/questions/17480044/how-to-install-the-current-version-of-go-in-ubuntu-precise]

# Creating a Simple Program
- **PROJECT #1 Example**

Now that you have the Go workspace set up, create a “Hello, World!” program.
```
nano hello.go
```
Write your program in the **hello.go** file:
```
package main

import "fmt"

func main() {
	fmt.Println("Hello, World!")
}
```
This code will use the fmt package and call the Println function with Hello, World! as the argument.
return to your shell, run the program:
```
go run hello.go
```
- **PROJECT #2 Example**

To create the functionality of the Hello World application, add the following Go code into the newly created main.go file:
```
nano main.go
```
```
package main

import (
	"fmt"
	"net/http"
)

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		fmt.Fprintf(w, "Hello World")
	})

	http.HandleFunc("/greet/", func(w http.ResponseWriter, r *http.Request) {
		name := r.URL.Path[len("/greet/"):]
		fmt.Fprintf(w, "Hello %s\n", name)
	})

	http.ListenAndServe(":9990", nil)
}
```
This include the standard fmt package and the net/http package for your web server.
The next snippet creates your first route in the main function, which is the entry point of any Go application.
A parent route / is created within func main, which will return the text Hello World when requested.
The preceding snippet starts the server and exposes your application via port 9990 using Go’s inbuilt http server.

return to your shell, run the program:
```
go run hello.go
```
Next, build the binary executable of your application by running:
```
go build main.go
```
The preceding command will compile main.go to produce an executable titled main.

# Creating systemd service
Next, you will create a systemd unit file to keep your application running in the background even when you are not accessing your server.
**First**, create a new file in **/lib/systemd/system** directory named **goweb.service** using **nano**.
```
sudo nano /lib/systemd/system/goweb.service
```
To set the parameters of the service, add the following snippet into the file.
```
[Unit]
Description=Go sample web app
After=multi-user.target

[Service]
User=root
Group=root
WorkingDirectory=/root/go/hello_world/
ExecStart=/bin/bash -lc 'go run main.go'

[Install]
WantedBy=multi-user.target
```
Next, start and enable this service:
```
systemctl daemon-reload
systemctl start hello_world.service
systemctl enable hello_world.service
```
In this **cheatsheet** you will find a collection of common commands used with the command line tools **systemctl**:
![systemd Cheat Sheet](https://user-images.githubusercontent.com/71556060/182783157-73de3da0-1065-497d-af3b-22e008363648.jpg)
# Setting up a reverse proxy server
I will use Nginx as a reverse proxy server, but you can choose any other one.
Create a config in /etc/nginx/sites-available/hello_world file:
```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    server_name _;

    location /root/go/hello_world {
            proxy_pass http://localhost:9990;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'upgrade';
            proxy_set_header Host $host;
            proxy_cache_bypass $http_upgrade;
    }
}
```
Create a symbolic link of our config file to the sites-enabled folder:
```
ln -s /etc/nginx/sites-available/hello_world /etc/nginx/sites-enabled
```
reload nginx to apply config.
```
nginx -t && nginx -s reload
```
Finally, apply SSL certbot:
```
certbot --nginx
```
# Final result
Yep, that's it! Go to http://your_server_ip:9990 and see result 🎉
If you want more articles like this on this blog, then post a comment below and subscribe to me. Thanks! 😘

