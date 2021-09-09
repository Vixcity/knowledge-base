1. 通过 effect 的 第二个参数给定的一个 scheduler 的 fn
2. 当 effect 第一次执行的时候，还会执行 fn
3. 当响应式对象 set updata 的时候，不会执行 fn ，而是执行 scheduler
4. 当执行 runner 的时候，会再次执行 fn