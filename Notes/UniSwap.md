# UniSwap

## UniSwap 的基本模式

在中心化交易所，用户间挂买单和卖单，交易所的撮合引擎会按时间和价格进行排序，一旦买单和卖单价格重叠，就可以成交完成交换。但 Uniswap 里没有挂单这个设计，取而代之的是**流动性资金池充当所有交易的对手盘**。

流动性资金池是包含了需要交易的两种币，比如 ETH 和 USDT。比如一个流动性池里有 x 个 ETH 和 y 个 USDT。此时 k=x * y （k 是一个常数）当用户 A 想用 dy 个 USDT 买 ETH，**在不考虑滑点的前提下**，用户 A 买入的价格是 y/x。而真实成交时，用户能买到的 ETH 数量 dx 为 (不考虑 0.3% 的手续费）：k=(x-dx)*(y+dy)，dx=x-k/(y+dy)交易所，流动性池里的资金就成了：（x-dx）个 ETH 和（y+dy）个 USDT，而此时 ETH 的价格变成了 (y+dy)/(x-dx)。

这既是 UniSwap 的交易基本模式。

## LP Token 的铸造

在 Uniswap 里对某个交易对（pair) 第一次添加流动性的人，可以任意对交易对的价格进行定价，即 x、y 和 k 都是这个人定的。创世流动性之后再添加 dx 和 dy，如果按系统默认值去添加，**都会受 dx/dy=x/y 的约束**。添加流动性后，Uniswap 会给用户返回一个 erc20 代币，这就是 LP Token。

**LP Token 的总量是变化的**，添加流动性就会铸造新的 LP Token，即**增发**，而**赎回流动性则会销毁 LP Token**。

## UniSwap 中的 K 值的设计

Uniswap 里第一个人添加 x 个 ETH 和 y 个 USDT 的作为流动性时，决定了 K 值的初始大小，即 K=x*y。

添加完流动性后，ETH 的价格就等于 =y(USDT 的数量)/x(ETH 的数量)。如果这个价格和其他交易所之间有价差，那肯定会被人搬砖套利。

所谓的恒定乘积算法，指的是在流动性池没有再添加或减少流动性的情况下，只有交易行为发生的情况下，K 值是不变的。

但是，由于交易费的存在，其实 K 值并不是恒定的：

用户使用 Uniswap 交易时，需要交 0.3% 的手续费。比如用户拿 dy 个 usdt 买 ETH，Uniswap 会首先扣除 0.3%dy 的手续费，先将这 0.3%dy 的手续费丢在一边，完成交易后，这 0.3%dy 会被添加到流动性池里，此时 K 值就变成了 x*(y+0.3%dy)。

## UniSwap 的 code review

![](https://img.chainnews.com/material/images/809e4ba409f2586983b765945ff2aada_9QDzBys.jpg-article)