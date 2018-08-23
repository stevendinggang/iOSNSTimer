# iOSNSTimer
iOS里面的4种不同的定时器使用  1. NSTimer  2. CADisplayLink 3. GCD 4. NSObject 

    //1. 使用timer
    self.time1 = [NSTimer timerWithTimeInterval:5.0 target:self selector:@selector(test) userInfo:nil repeats:YES];
    // 注意点: 这里 runloop 需要使用, commonmodes,保证timer 调用正常
    [[NSRunLoop currentRunLoop] addTimer:self.time1 forMode:NSRunLoopCommonModes];

   // 2.使用 CADisplayLink, 只能使用 每帧 调用,并不能自己设置时间
    self.time2 = [CADisplayLink displayLinkWithTarget:self selector:@selector(test)];
    //   self.time2.frameInterval = 3600;
    //   self.time2.preferredFramesPerSecond = 0.1;
    [self.time2 addToRunLoop:[NSRunLoop currentRunLoop] forMode:NSRunLoopCommonModes];

   // 使用gcd 进行定时


  //一.创建定时器对象
    //这个方法只要敲:(dispatach_source...timer..)
    //01参数:要创建的source 是什么类型的
    //(DISPATCH_SOURCE_TYPE_TIMER)定时器
    //04参数:队列  ----线程  决定block 在哪个线程中调用
    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    
    //二.设置定时器
    //01参数:定时器对象
    //02参数:开始时间 (DISPATCH_TIME_NOW) 什么时候开始执行第一次任务
    //03参数:间隔时间 GCD时间单位:纳秒
    //04参数:leewayInSeconds精准度:允许的误差: 0 表示绝对精准
    dispatch_source_t timer =dispatch_source_create(DISPATCH_SOURCE_TYPE_TIMER,0, 0, queue);
    
     //三.定时器每隔一段时间就要执行任务(block回调)
    dispatch_source_set_timer(timer, dispatch_walltime(NULL, 0), (uint64_t)(timeInterval *NSEC_PER_SEC), 0);
    
    // 设置回调
    __weak __typeof(target) weaktarget  = target;
    dispatch_source_set_event_handler(timer, ^{
        if (!weaktarget)  {
            dispatch_source_cancel(timer);
        } else {
            dispatch_async(dispatch_get_main_queue(), ^{
                if (handler) handler(timer);
            });
        }
    });
    // 启动定时器
    dispatch_resume(timer);
}



//  4.定时器 NSObject中的performSelector:withObject:afterDelay:方法将会在当前线程的Run Loop中根据afterDelay参数创建一个Timer，如果没有调用有inModes参数的方法，该Timer会运行在当前Run Loop的默认模式中，也就是NSDefaultRunLoopMode定义的模式中。

-(void)useNSObjectPerformSelector{
    //1. 延时调用
    [NSObject performSelector:@selector(test) withObject:self afterDelay:20];
    //2. 取消调用任务
    [NSObject cancelPreviousPerformRequestsWithTarget:self selector:@selector(test) object:nil];





代码结构: 不好意思....中文命名了
  

