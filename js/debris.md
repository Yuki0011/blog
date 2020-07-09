```
//数字类型过长转换精度丢失例子+解决
<script type="text/javascript">

var json = '{"beginDate":"2018-03-02","beginTime":"02:00","eventName":"2020/4/21 航线存储2","intelligent":0,"routeId":1250823228691927041,"routeJson":"[[37.8,121.9],[37.8,121.81552018192154],[37.8,122.13104036384308],[37.8,122.44656054576464],[37.750947803431856,122.7],[37.501644057137725,122.7],[37.3,122.64009034443816],[37.08258916539395,122.6],[36.9,122.51658054369804],[36.8,122.32997868282284],[36.8,122.01863570066196],[36.645692210942414,121.9],[36.396388464648275,121.9],[36.2,121.9]]","speed":15.0,"type":1}';
	
var newResponseString = json.replace(/\"routeId\":(\d+)/,'"routeId":"$1"');   //将航线中routeId 替换成 字符串 防止过长后转换数字失效
var resultObj = JSON.parse(json);
var resultObj2 = JSON.parse(newResponseString);
console.log(resultObj);
console.log(resultObj2);

</script>

```