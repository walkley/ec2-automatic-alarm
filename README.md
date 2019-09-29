# ec2-automatic-alarm
所有Amazon EC2创建后都会有相应的监控指标，如CPU利用率、网络流量等，如果想自动监控这些指标并在指标超过或低于阈值时自动报警，如发送邮件短信等，则可以配置这些监控指标相关的CloudWatch Alarm,设置阈值和报警动作，调用SNS发送邮件或短信等。但如果需要大量的EC2运行，或者EC2频繁创建销毁时，手动创建维护这些CloudWatch Alarm会费事费力，本人演示如何自动创建Amaazon EC2相关指标的告警，在EC2创建时自动创建相关的CloudWatch Alarm，在EC2终止时自动删除CloudWatch Alarm.

## 工作原理
EC2在状态方式变化，如running, terminated等时候都会有CloudWatch Event发生，可以配置CloudWatch Event触发是自动执行的对象，如SNS、Lambda等。本文演示的方案会创建CloudWatch Rule，在EC2状态变为running或terminated时会接收event并自动调用Lambda，running状态时Lambda会创建这个EC2实例相关的CloudWatch Alarm，Alarm的触发目标是事先创建好的SNS，terminated状态时Lambda会自动删除这个EC2实例相关的CloudWatch Alarm.

## 部署
1. 创建SNS主题和订阅，设置通知形式如短信等。
2. 下载[CloudFormation模板文件](https://raw.githubusercontent.com/walkley/ec2-automatic-alarm/master/template.yaml)，在AWS控制台中打开CloudFormation，创建CloudFormation stack，上传CloudFormation文件，在参数中填写SNS ARN.
3. 目前CloudWatch Alarm相关的触发参数固定写在CloudFormation模板文件中，如需调整可以修改模板文件或者提前参数。
