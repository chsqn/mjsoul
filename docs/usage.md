# **Usage**

MJSoul继承node的EventEmitter类，可以使用on方法来监听服务器通知。  
若服务器响应数据中包含error字段，则会触发reject，包括雀魂自己定义的error和追加的三个  
* 9999 连接服务器失败，将再一次尝试连接
* 9998 接口不存在
* 9997 响应超时

### **基本用法:**

```js
const MJSoul = require("mjsoul")
const mjsoul = new MJSoul()
let login = async()=>{
    //登陆
    let data = await mjsoul.sendAsync(
        "login",
        {account: "account", password: mjsoul.hash("password")} //用户名和密码。
    )
    console.log(data)
    //获取好友列表
    data = await mjsoul.sendAsync("fetchFriendList")
    console.log(data)
}
//绑定事件
mjsoul.on("NotifyAccountLogout", login)
mjsoul.open(login)

//注意：日服或国际服只能使用token登陆，在浏览器控制台输入GameMgr.Inst.access_token可以获得
await mjsoul.sendAsync('oauth2Login', {
    type: 10,
    access_token: "token"
})
```

### **构造函数:**

```js
const MJSoul = require("mjsoul")
const mjsoul = new MJSoul({
    url: "wss://gateway-v2.majsoul.com:4501", //雀魂ws连接地址，可以修改为日服或者国际服的地址
    timeout: 5000, //异步超时时间，超时则会reject
    wsOption: "" //websocket参数(使用https://github.com/websockets/ws)，可以用来配置代理等
})

// wss://gateway-v2.maj-soul.com:5101 线路1(香港原始节点)
// wss://gateway-vexcdn.maj-soul.com/gateway 线路2(海外中转入口)
// wss://gateway-cdn.maj-soul.com/gateway 线路3(海外中转入口)
// wss://mjjpgs.mahjongsoul.com:4501 日服
// wss://mjusgs.mahjongsoul.com:4501 美服
// wss://gateway-v2.maj-soul.com:6001 雀魂大会室后台
// wss://mjjpgs.mahjongsoul.com:5201 日服大会室
// wss://mjusgs.mahjongsoul.com:5201 美服大会室
// 日服美服不支持账号密码登陆，只能使用token自动登陆(token不会过期)
```

### **大会室后台:**

*后台数据定义文件为dhs.json*

```js
const MJSoul = require("mjsoul")
const dhs = new MJSoul.DHS()
dhs.open(async()=>{
    let data = await dhs.sendAsync("loginContestManager", {account: "account", password: dhs.hash("password")})
    console.log(data)
})
```

### **解析牌谱:**

```js
MJSoul.record.parseById("game-uuid", (data)=>{
    console.log(data)
})
```
