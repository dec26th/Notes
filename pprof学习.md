# pprof学习

## Web上使用pprof

- 只需要import`_ "net/http/pprof"`即可
- 可以在网页上打开`http://localhost:9876/debug/pprof`
- 可以输入`http://localhost:9876/debug/pprof/profile`来下载profile文件，然后通过命令`go tool pprof test profile`来打开文件
  - top指令 可以获取占用前10 的数据（cpu）
    - flat、flat% 表示函数在 CPU 上运行的时间以及百分比
    - sum% 表示当前函数累加使用 CPU 的比例
    - cum、cum%表示该函数以及子函数运行所占用的时间和比例，应该大于等于前两列的值
  - Web 可以在网页上看到图形化的界面
- 或者使用`go-torch`工具，可以看到火焰图
  - 火焰图中调用顺序从下往上
  - 图形越长说明占用CPU时间越长
  - 每一个方块代表一个函数
  - 上面一层表示当前层调用的函数
- 可以使用`/debug/pprof/heap`来获得程序的内存快照

## 正常程序使用pprof

- 引入`runtime/pprof`

- ```go
  // 生成 CPU 报告
  func cpuProfile() {
  	f, err := os.OpenFile("cpu.prof", os.O_RDWR|os.O_CREATE, 0644)
  	if err != nil {
  		log.Fatal(err)
  	}
  	defer f.Close()
  
  	log.Println("CPU Profile started")
  	pprof.StartCPUProfile(f)
  	defer pprof.StopCPUProfile()
  
  	time.Sleep(60 * time.Second)
  	fmt.Println("CPU Profile stopped")
  }
  
  // 生成堆内存报告
  func heapProfile() {
  	f, err := os.OpenFile("heap.prof", os.O_RDWR|os.O_CREATE, 0644)
  	if err != nil {
  		log.Fatal(err)
  	}
  	defer f.Close()
  
  	time.Sleep(30 * time.Second)
  
  	pprof.WriteHeapProfile(f)
  	fmt.Println("Heap Profile generated")
  }
  ```

  