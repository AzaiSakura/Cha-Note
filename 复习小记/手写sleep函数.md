## 手写一个Sleep函数的方式~

```js
//1 通过Promise 实现
console.log('A');
function sleep(delay) {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve()
        }, delay)
    })
}
sleep(1000).then(() => {
    console.log('B');
})

//2 通过async/await实现
function sleep2(delay) {
    return new Promise((resolve,reject) => {
        setTimeout(() => {
            resolve();
        },delay)
    }).then(() => {
        console.log("C")
    })
}
async function sleepAsync() {
    await sleep2(2000);
}
sleepAsync();

//3 根据Generator配合yield进行实现
const sleep3 = ((delay) => {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve();
        },delay)
    })
})
function* sleepGenerator(delay) {
    yield sleep3(delay) ;
}
sleepGenerator(3000).next().value.then(() => {
    console.log("D");
})


```

