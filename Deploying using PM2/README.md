![firefox_jQjD6leU1n](https://user-images.githubusercontent.com/71556060/182789640-b44fcdf5-6589-4226-8652-4544f26baa3b.png)

# PM2 as an Alternative!
PM2 is the Process Manager which is built for Node.js application.
# PM2 limited to the Node.js app?
NO! It is true that PM2 is intended to serve Node.js application but it is not limited to the Node.js runtime. After I used PM2 for some time,
I realized that it can be used to manage any programming script/runtime! From that moment,
I tried it with Python and GO and it works just fine!

# 1-PM2 Installation
Installing can be also done in one line which you can either use npm or yarn .
```
npm install pm2@latest -g
# OR 
yarn global add pm2
# Check if program runs.
pm2 -v
```
**Running Your Project with PM2**
After installation is done, you can start any python script through the PM2 by using command.
```
pm2 start <script name>
```
Disclamer: The golang runtime is of course different that Node.js so we didn’t implement all the features availables for Node.js in the Golang agent,
here are the list of the missing features:
- Transaction tracing
- Memory Profiling (you should use Memory snapshot for Golang)

# 2-Integrate the code of PM2
You need to import our library, called pm2-io-apm-go, that we use to add metrics into your code and enable other features.
```
package main

import (
  "github.com/keymetrics/pm2-io-apm-go/services"
  "github.com/keymetrics/pm2-io-apm-go/structures"
)

func main() {
  // Create PM2 connector
  pm2 := pm2io.Pm2Io{
    Config: &structures.Config{
      PublicKey: "myPublic",    // define the public key given in the dashboard
      PrivateKey: "myPrivate",  // define the private key given in the dashboard
      Name: "Golang app",       // define an application name
    },
  }

  // Start the connection to PM2 Enterprise backend
  pm2.Start()
}
```
And that’s it, in your **main.go** file, then application will automatically connect to **PM2**. 

# 3-Running Your Project with PM2
After installation and integration, you can start script through the PM2 by using command on terminal.
```
pm2 start
```

https://pm2.io/docs/enterprise/collector/go/

