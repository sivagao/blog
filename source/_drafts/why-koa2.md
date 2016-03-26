我们为什么选择koa2来写我们的后端代码，我们可以从官方文档看出。然而koa2又让 es7 的async/await 直接可以用在我们的代码编写上，再也不用co来包装函数，用 *，yield 这些看起来语义很奇怪的generator语法了

### 异步代码
一些例子展示（从search.js等中摘取

```js
  var {storeUserInsInfo, storeLatestInfo, storeHotInfo} = apiConf;
  var {externalInfo, portSelfInfo, portSelfStocks} = apiConf;
  
  ## externalInfoFetch，storeInfoFetch等都是用于规整请求的入参
  
  router.get('/opt-auth/pickup', async (ctx, next)=>{
    var userId = ctx.request.get('userId');
    var reses = await Promise.all(_.map([
      externalInfoFetch('ZCCJTT'), // 外部精选资讯
      storeInfoFetch(storeLatestInfo),
      storeInfoFetch(storeUserInsInfo, userId)
    ], (opt)=>{
      return rp(opt);
    }));
    ctx.body = {
      "external-headline": reses[0],
      "store-latest": reses[1],
      "store-myinterest": reses[2]
    };
  });
```

### 错误处理

callback: domain, 异步错误需要next(err)，同步异常的try-catch等， domain被废弃掉，app.use((err, req, res, next))的忽略
所以顶层的try-catch 的放置顺序

```js
// error handler to JSON stringify errors
const errorRes = require('./middleware/error-res');
app.use(errorRes);

module.exports = async function(ctx, next) {
  try {
    await next();
  } catch (err) {
    if (err == null) {
      err = new Error('Null or undefined error');
    }
    // some errors will have .status
    // however this is not a guarantee
    ctx.status = err.status || 500;
    ctx.type = 'application/json';
    ctx.body = {
      success: false,
      message: err.stack
    };
    ctx.app.emit('error', err, this);
  }
};

// 用于关闭前的一些处理如保存数据，记录错误，发送邮件等等
process.on('uncaught', ()={});
```

### 中间件写法

对于response-time的
对于koa-composite



