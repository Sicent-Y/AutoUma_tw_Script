# AutoUma_tw_Script
赛马娘凹种马根本不是人干的事，由于没有找到免费的、手机版、繁中服的自动化脚本，遂自己动手编写了一个超级简陋的。

# 功能
- 该脚本**单次运行**只能进行一次**单轮育成**。
- 进入育成后可以自动读取训练、状态，自动选择训练、休息、外出或前往比赛，循环执行78个回合完成一轮育成
- 脚本使用**adb**进行操作，需要用电脑连接手机，控制手机自动游玩赛马娘游戏；adb连接的该设备**不应**被操作、打断(包括弹出通知、切换屏幕),**否则将会卡死**
- 代码识别范围狭窄，**不能够**自动选择剧本、培育马娘、种马与支援卡。请手动操作，将页面导航至**开始育成前的最后一步**再运行代码(也就是确认支援卡后，弹出的确认开始育成弹窗，确保体力足够进行一次育成；此时再运行代码，**否则将会卡死**；详细内容见后)
- 代码运行将在育成完成获取技能的界面处**结束**，之后需要手动查收；代码执行完成后会自动让手机熄屏
- 具体使用方法，会在后面的使用方法里介绍

# 原理
- 通过adb连接手机，截取手机屏幕进行判断，随后模拟点击
- 通过`opencv`库的`matchtemplate`函数进行模板匹配，通过访问`source\data\`文件夹内存储的模板图像，匹配手机截屏，以此判断页面、训练信息、按钮位置等
  - 注意：模板匹配对分辨率敏感，该脚本在1080x2400分辨率下开发，请确保你的设备分辨率一致，否则将会卡死；详细内容见后
- 每一回合会根据获取的信息，按照一定的算法，计算各项操作的权重，最后选取权重最高者执行(休息、外出、保健室、五种训练、比赛)
  具体算法如下：
  ```
  Speed:
  return (max(((100+(((Speed.Aim[catg]-Speed.Aim[catg-1])/stepLen)*(stage-sStage)))-Speed.Value),0))*Speed.Add*0.0013 + 0.06*(Speed.Add-9) + 0.15*Speed.Be3 + 0.3*Speed.Be4 - int(Speed.Aim[catg+1]<Speed.Value)*0.7 - max((Speed.Value-Speed.Aim[catg]),0)*0.005
  
  Stamina: 
  for check in range(1,catg+1):
    weight += int(Stamina.Aim[check]>Stamina.Value)*Stamina.Add*0.025
  return weight + 0.06*(Stamina.Add-9) + 0.15*Stamina.Be3 + 0.3*Stamina.Be4 - int(Stamina.Aim[catg]<Stamina.Value)*0.42 - int(Stamina.Aim[catg+1]<Stamina.Value)*1

  ```
