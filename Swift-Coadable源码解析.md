# Swift-Coadable源码解析


### Codable常见用法

#### 嵌套的模型
```swift
struct LGTeacher: Codable{
    var name: String
    var className: String
    var courceCycle: Int
    var personInfo: PersonInfo
}

extension LGTeacher {
    struct PersonInfo: Codable {
        var age: Int
        var height: Double
    }
}

let jsonString = """
{
    "name": "Kody",
    "className": "Swift",
    "courceCycle": 10,
    "personInfo": {
        "age": 18,
        "height": 1.85
     }
}
"""

let jsonData = jsonString.data(using: .utf8)
let decoder = JSONDecoder()
if let data = jsonData{
    let result = try? decoder.decode(LGTeacher.self, from: data)
    print(result ?? "解析失败")
}
```
<a name="mVOS6"></a>
#### JSON数据中包含数组
```swift
struct LGTeacher: Codable{
    var name: String
    var className: String
    var courceCycle: Int
    var personInfo: [PersonInfo]
}

extension LGTeacher {
    struct PersonInfo: Codable {
        var age: Int
        var height: Double
    }
}

let jsonString = """
{
    "name": "Kody",
    "className": "Swift",
    "courceCycle": 10,
    "personInfo": [
        {
           "age": 18,
           "height": 1.85
        },{
           "age": 20,
           "height": 1.75
        }
    ]
}
"""

let jsonData = jsonString.data(using: .utf8)
let decoder = JSONDecoder()
if let data = jsonData{
    let result = try? decoder.decode(LGTeacher.self, from: data)
    print(result ?? "解析失败")
}
```
<a name="OhtTo"></a>
#### JSON数据是一组数组集合


```swift
struct LGTeacher: Codable{
    var name: String
    var className: String
    var courceCycle: Int
}


let jsonString = """
[
    {
        "name": "Kody",
        "className": "Swift",
        "courceCycle": 12
    },{
        "name": "Cat",
        "className": "强化班",
        "courceCycle": 15
    },{
        "name": "Hank",
        "className": "逆向班",
        "courceCycle": 22
    },{
        "name": "Cooci",
        "className": "大师班",
        "courceCycle": 22
    }
]
"""

let jsonData = jsonString.data(using: .utf8)
let decoder = JSONDecoder()
if let data = jsonData{
    let result = try? decoder.decode([LGTeacher].self, from: data)
    print(result ?? "解析失败")
}
```
<a name="DkSUw"></a>
#### JSON数据中有 **Optional values**
**
```swift
let jsonString = """
[
    {
        "name": "Kody",
        "className": "Swift",
        "courceCycle": 12
    },{
        "name": "Cat",
        "className": "强化班",
        "courceCycle": 15
    },{
        "name": "Hank",
        "className": null,
        "courceCycle": 22
    },{
        "name": "Cooci",
        "className": "大师班",
        "courceCycle": 22
    }
]
"""

let jsonData = jsonString.data(using: .utf8)
let decoder = JSONDecoder()
if let data = jsonData{
    let result = try? decoder.decode([LGTeacher].self, from: data)
    print(result ?? "解析失败")
}
```
![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609073115976-54e0959f-995b-4aa6-93f8-fcdb9ea6e57b.png#align=left&display=inline&height=735&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1470&originWidth=2110&size=980587&status=done&style=none&width=1055)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609073142133-d0821bb6-a391-49e3-892b-1955058065ee.png#align=left&display=inline&height=466&margin=%5Bobject%20Object%5D&name=image.png&originHeight=932&originWidth=1886&size=592523&status=done&style=none&width=943)
<a name="RHLLS"></a>
#### **元组类型**
比如我们有一个坐标，`location : [20, 10]`，当我们在使用`Codable`进行解析的过程中，我们需要进行如下操作：
```swift
struct Location: Codable {
    var x: Double
    var y: Double
    
    init(from decoder: Decoder) throws{
        var contaioner = try decoder.unkeyedContainer()
        
        self.x = try contaioner.decode(Double.self)
        self.y = try contaioner.decode(Double.self)
    }
}

struct RawSeverResponse: Codable{
    var location: Location
    
}

let jsonString = """
{
    "location": [20, 10]
}
"""

let jsonData = jsonString.data(using: .utf8)
let decoder = JSONDecoder()
let result = try decoder.decode(RawSeverResponse.self, from: jsonData!)
print(result.location.x)
```
![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609073618871-5a72d449-d6cc-4c51-8554-1fc1393bc18e.png#align=left&display=inline&height=451&margin=%5Bobject%20Object%5D&name=image.png&originHeight=902&originWidth=1752&size=557179&status=done&style=none&width=876)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609073626253-177a452c-509e-4a5b-9391-3d74cb272a5a.png#align=left&display=inline&height=363&margin=%5Bobject%20Object%5D&name=image.png&originHeight=726&originWidth=1878&size=483168&status=done&style=none&width=939)<br />

<a name="836gv"></a>
#### 嵌套的数据模型


<a name="tIsC0"></a>
#### 继承
```swift
class LGTeacher: Codable {
    var name: String?
}

class LGPartTimeTeacher: LGTeacher {
    var partTime: Int?
}


let jsonString = """
{
    "name": "Kody",
    "partTime": 20
}
"""

let jsonData = jsonString.data(using: .utf8)
let decoder = JSONDecoder()
let result = try decoder.decode(LGPartTimeTeacher.self, from: jsonData!)
print(result.name)
```


```swift
protocol LGTeacher {
    var name: String{ get set }
}
//
struct LGPartTimeTeacher: LGTeacher, Codable {
    var name: String
    var partTime: Int?
}
//
//
let jsonString = """
{
    "name": "Kody",
    "partTime": 20
}
"""
//
let jsonData = jsonString.data(using: .utf8)
let decoder = JSONDecoder()
let result = try decoder.decode(LGPartTimeTeacher.self, from: jsonData!)
print(result)
```


<a name="rUvxO"></a>
#### 不方便的数组类型
```swift
struct LGPerson: Decodable{
    
    let elements: [String]
    
    enum CodingKeys: String, CaseIterable, CodingKey {
        case item0 = "item.0"
        case item1 = "item.1"
        case item2 = "item.2"
        case item3 = "item.3"
    }
    
    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        
        var element: [String]  = []
        
        for item in CodingKeys.allCases{
            guard container.contains(item) else { break }
            
            element.append(try container.decode(String.self, forKey: item))
        }
        
        self.elements = element
    }
}
//
//
let jsonString = """
{
    "item.3": "Kody",
    "item.0": "Hank",
    "item.2": "Cooci",
    "item.1": "Cat"
}
"""

let jsonData = jsonString.data(using: .utf8)
let decoder = JSONDecoder()
let result = try decoder.decode(LGPerson.self, from: jsonData!)
print(result)
```
![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609076477716-25b459fb-68db-4ee5-b6cc-a21633af92bc.png#align=left&display=inline&height=409&margin=%5Bobject%20Object%5D&name=image.png&originHeight=818&originWidth=1972&size=735760&status=done&style=none&width=986)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609076486079-0fdc2165-2761-4a93-8060-e83d897d818e.png#align=left&display=inline&height=698&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1396&originWidth=2200&size=907974&status=done&style=none&width=1100)<br />

<a name="QkUwd"></a>
### Codable源码解析


<a name="JxbDi"></a>
#### 解码处理流程

<br />我们先来看一下 `Codable` 到底是什么？<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608611651225-73a178fc-ed58-4c57-9e0b-efade279191a.png#align=left&display=inline&height=181&margin=%5Bobject%20Object%5D&name=image.png&originHeight=362&originWidth=1736&size=350783&status=done&style=none&width=868)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608612635658-a2df4d7e-d7cd-474d-b875-d0398687a3a4.png#align=left&display=inline&height=295&margin=%5Bobject%20Object%5D&name=image.png&originHeight=590&originWidth=1542&size=401423&status=done&style=none&width=771)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608612645740-782ed241-16c5-45e3-82ed-074cc5d0eb76.png#align=left&display=inline&height=372&margin=%5Bobject%20Object%5D&name=image.png&originHeight=744&originWidth=1554&size=541769&status=done&style=none&width=777)<br />
<br />这里我们使用一个简单的案例来看一下：
```swift
struct LGTeacher: Codable {
    var name: String
    var className: String
    var courceCycle: Int
}

let jsonString = """
{
    "name": "Kody",
    "className": "Swift",
    "courceCycle": 10
}
"""

let jsonData = jsonString.data(using: .utf8)
let decoder = JSONDecoder()
if let data = jsonData{
    let result = try? decoder.decode(LGTeacher.self, from: data)
    print(result ?? "解析失败")
}
```
当前我们创建一个解码的对象，然后调用 `decode` 方法将我们的 `json` 字符串解析给我们的模型 `LGTeacher` 。这里我们需要探究的是它究竟是如何工作的？<br />
<br />第一：我们先来看一下 `JSONDecoder` 创建出来的对象
```swift
public enum DateDecodingStrategy {

        /// Defer to `Date` for decoding. This is the default strategy.
        case deferredToDate
    
    	/// 代表距离 1970.01.01 的秒数
        /// Decode the `Date` as a UNIX timestamp from a JSON number.
        case secondsSince1970

    	/// 代表距离 1970.1.1 的毫秒数
        /// Decode the `Date` as UNIX millisecond timestamp from a JSON number.
        case millisecondsSince1970

        /// Decode the `Date` as an ISO-8601-formatted string (in RFC 3339 format).
        @available(OSX 10.12, iOS 10.0, watchOS 3.0, tvOS 10.0, *)
        case iso8601
		
    	/// 后台自定义的格式，这个时候我们可以自己创建 DateFormatter，来解析
        /// Decode the `Date` as a string parsed by the given formatter.
        case formatted(DateFormatter)
       
    	/// 自定义格式
        /// Decode the `Date` as a custom value decoded by the given closure.
        case custom((Decoder) throws -> Date)
    }
```
这里我们来看一下实际的使用场景：
```swift
struct LGTeacher: Codable {
    var name: String
    var className: String
    var courceCycle: Int
    var date: Date
}

let jsonString = """
{
    "name": "Kody",
    "className": "Swift",
    "courceCycle": 10
    "date": "1969-09-26T12:00:00Z"
}
"""
```
如果我们直接使用默认的解析策略，那么这里代码运行之后，就会出现解析失败
```swift
decoder.dateDecodingStrategy = JSONDecoder.DateDecodingStrategy.iso8601
```
```swift
let jsonString = """
{
    "name": "Kody",
    "className": "Swift",
    "courceCycle": 10,
    "date": 1609183207
}
"""
```
```swift
decoder.dateDecodingStrategy = .secondsSince1970
```
```swift
let jsonString = """
{
    "name": "Kody",
    "className": "Swift",
    "courceCycle": 10,
    "date": 1609183207000
}
"""

decoder.dateDecodingStrategy = .millisecondsSince1970
```


```swift

let jsonString = """
{
    "name": "Kody",
    "className": "Swift",
    "courceCycle": 10,
    "date": "2020/12/28 19:20:00"
}
"""

这种后台自定义的格式，需要我们创建一个DateFormatter

let dateFormatter = DateFormatter()
dateFormatter.dateFormat = "yyyy/MM/dd HH:mm:ss"
decoder.dateDecodingStrategy = .formatted(dateFormatter)
```
![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608967062407-43219fd7-9be2-454d-96b8-2c13734a9a67.png#align=left&display=inline&height=260&margin=%5Bobject%20Object%5D&name=image.png&originHeight=520&originWidth=1876&size=130840&status=done&style=none&width=938)<br />以上就是 `JSONDecoder` 的主要内容，定义了编码的策略，可以让我们根据不同的场景来进行选择。<br />
<br />接下来我们实际看一下当前是如何 `decode` 的<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608961529487-63b7ee6c-3592-495f-9e77-f7d5ca9cd47b.png#align=left&display=inline&height=426&margin=%5Bobject%20Object%5D&name=image.png&originHeight=852&originWidth=2434&size=1280571&status=done&style=none&width=1217)

- 这里是一个泛型函数，传入的参数 `T` 要求遵守 `Decodable` 协议。
- 调用 `JSONSerializationg` 对当前 `data` 进行序列话的操作
- 调用内部类 `_JSONDecoder` 创建一个对象，然后调用 `unBox` 解码


<br />这里我们关注第三步和第四步，首先从第三步说起：这里返回了一个`Decoder`的对象。<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608968056149-e0e90ea3-9257-4ebc-a11e-4c77428a7edf.png#align=left&display=inline&height=701&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1402&originWidth=2234&size=1307539&status=done&style=none&width=1117)<br />
<br />其中 `storage` 的实现：<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608968109480-58b63447-c6a6-4525-949c-c9abc5349b8b.png#align=left&display=inline&height=719&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1438&originWidth=1986&size=1140898&status=done&style=none&width=993)<br />回到我们的第四步，`unBox` 就是开始拆盒子<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608968531837-a56e65c8-8e7f-4330-bc2f-9bdcb922e355.png#align=left&display=inline&height=508&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1016&originWidth=2504&size=1497735&status=done&style=none&width=1252)<br />可以看到这里就是匹配对应的类型，然后执行条件分支<br />
<br />回到我们当前的`main.swift` ，这个时候我们并没有实现任何的方法<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608969517514-798b6e63-77ae-4c17-9344-bf8e18b6ff4c.png#align=left&display=inline&height=331&margin=%5Bobject%20Object%5D&name=image.png&originHeight=662&originWidth=1728&size=410900&status=done&style=none&width=864)<br />那么这里到底发生了什么？我们借助我们的老朋友`SIL`来看一下：<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609058280350-38e0f6b4-20ac-406c-977d-ca16fde475b8.png#align=left&display=inline&height=481&margin=%5Bobject%20Object%5D&name=image.png&originHeight=962&originWidth=1876&size=1069311&status=done&style=none&width=938)<br />也就意味着，当前编译器自动生成了一个默认的`init(from:)` 实现。我们这里来一起阅读一下，发生了什么事情：<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608969573940-b29da8c5-5232-4e07-9959-32a9bccaa2de.png#align=left&display=inline&height=684&margin=%5Bobject%20Object%5D&name=image.png&originHeight=684&originWidth=1710&size=887799&status=done&style=none&width=1710)<br />
<br />那查找的协议方法是什么？我们回到刚才的`_JSONDecoder` 的实现：<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608969579566-129a506e-4f98-46e8-b417-c829030a0bba.png#align=left&display=inline&height=162&margin=%5Bobject%20Object%5D&name=image.png&originHeight=324&originWidth=1646&size=207340&status=done&style=none&width=823)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608969619123-a1b88232-ca32-49d4-968c-75eda13b4d3f.png#align=left&display=inline&height=1338&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1338&originWidth=2318&size=1483325&status=done&style=none&width=2318)<br />
<br />我们来比对一下:<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608969616037-03852426-3527-482c-8b40-473ae03cb608.png#align=left&display=inline&height=462&margin=%5Bobject%20Object%5D&name=image.png&originHeight=924&originWidth=1790&size=555901&status=done&style=none&width=895)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608969616027-688d06bb-fc1c-4e97-a78d-6f41d5d9b124.png#align=left&display=inline&height=77&margin=%5Bobject%20Object%5D&name=image.png&originHeight=154&originWidth=1650&size=149724&status=done&style=none&width=825)<br />这里就是再调用当前的找到的`container` 方法的实现；而这个方法在哪里实现了？是不是就是在`_JSONDecoder`里面，我们下一个断点看一下<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608969687668-f3554475-a740-4e0e-a0a5-45bd61a3336d.png#align=left&display=inline&height=420&margin=%5Bobject%20Object%5D&name=image.png&originHeight=840&originWidth=2558&size=1148379&status=done&style=none&width=1279)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608969751457-441f7098-6695-4ec8-b524-d5124905eb42.png#align=left&display=inline&height=471&margin=%5Bobject%20Object%5D&name=image.png&originHeight=942&originWidth=2444&size=1173844&status=done&style=none&width=1222)<br />
<br />接下来就有个问题了，是如何遍历我们当前的 `Key` 值，来进行`key-value` 的赋值操作？首先我们先看到 `SIL` 文件<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608988522284-8e465c9a-76e5-4d73-8823-e017d45caf16.png#align=left&display=inline&height=290&margin=%5Bobject%20Object%5D&name=image.png&originHeight=580&originWidth=1716&size=641274&status=done&style=none&width=858)<br />首先在内存当中创建了枚举类型`name`，然后调用`decode（）`方法，这个时候我们在回到我们的<br />
<br />找到`KeyedDecodingContainer`的具体实现：<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608988559166-49087d9f-b15e-4a57-9dc4-004ea428f0d5.png#align=left&display=inline&height=485&margin=%5Bobject%20Object%5D&name=image.png&originHeight=970&originWidth=1482&size=454576&status=done&style=none&width=741)<br />此时的`Container`是什么？是不是就是<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608988574459-00c59d25-90cc-4bfb-895f-77319c9f818b.png#align=left&display=inline&height=227&margin=%5Bobject%20Object%5D&name=image.png&originHeight=454&originWidth=2364&size=465994&status=done&style=none&width=1182)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608988583880-e081a4af-3e94-40a7-8826-e2b49efcb186.png#align=left&display=inline&height=459&margin=%5Bobject%20Object%5D&name=image.png&originHeight=918&originWidth=1664&size=443204&status=done&style=none&width=832)<br />这里的`Decode`方法是有`_box`调用的，我们回过头来在来找一下<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608988600083-650dd3d4-2b7d-4680-a97f-23b6308da14d.png#align=left&display=inline&height=308&margin=%5Bobject%20Object%5D&name=image.png&originHeight=616&originWidth=1206&size=273521&status=done&style=none&width=603)<br />基本上到这里我们就明白了，其实是在调用`_JSONKeyedDecodingContainer`的`decode`方法，这里我们直接通过断点的方式来确定一下：<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608988619246-5e013955-722e-4a67-b890-4d8ac18b313a.png#align=left&display=inline&height=417&margin=%5Bobject%20Object%5D&name=image.png&originHeight=834&originWidth=2508&size=1085823&status=done&style=none&width=1254)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1608988629853-b9d574fb-1d6e-4cdc-a480-8d22c0992d9d.png#align=left&display=inline&height=521&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1042&originWidth=1684&size=1143281&status=done&style=none&width=842)<br />

<a name="fwJCT"></a>
#### 编码流程处理
![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609127057451-4b2504f5-b149-48cc-9bcd-d9f618a9074f.png#align=left&display=inline&height=481&margin=%5Bobject%20Object%5D&name=image.png&originHeight=962&originWidth=1946&size=759823&status=done&style=none&width=973)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609127073155-913a5dd8-6d82-4f14-85a7-1adae6875f20.png#align=left&display=inline&height=358&margin=%5Bobject%20Object%5D&name=image.png&originHeight=716&originWidth=1850&size=625725&status=done&style=none&width=925)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609127080327-dea6387d-7097-4c67-8161-8b871a97909d.png#align=left&display=inline&height=258&margin=%5Bobject%20Object%5D&name=image.png&originHeight=516&originWidth=1878&size=699614&status=done&style=none&width=939)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609127188145-d1100f13-8e67-40e8-8d3f-2328fa7813ca.png#align=left&display=inline&height=273&margin=%5Bobject%20Object%5D&name=image.png&originHeight=546&originWidth=1804&size=601590&status=done&style=none&width=902)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609127196588-d59d1f08-f149-4842-8779-301707ff1d3c.png#align=left&display=inline&height=212&margin=%5Bobject%20Object%5D&name=image.png&originHeight=424&originWidth=1910&size=329815&status=done&style=none&width=955)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609127229218-716309a0-3956-464d-abb5-efa11b133a35.png#align=left&display=inline&height=508&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1016&originWidth=1882&size=861426&status=done&style=none&width=941)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609127240879-579016ab-e208-4368-be66-3eba09cfccbb.png#align=left&display=inline&height=260&margin=%5Bobject%20Object%5D&name=image.png&originHeight=520&originWidth=1860&size=559735&status=done&style=none&width=930)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609127276180-2523915c-b6d2-466d-8285-f236e990f8cb.png#align=left&display=inline&height=496&margin=%5Bobject%20Object%5D&name=image.png&originHeight=992&originWidth=1848&size=1190102&status=done&style=none&width=924)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609127276136-0b8b032c-7f66-4bf4-a355-b5a5083c5a73.png#align=left&display=inline&height=670&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1340&originWidth=1840&size=1095533&status=done&style=none&width=920)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609127424907-e1d01ca0-c2e0-41f8-87c5-4c4139c57125.png#align=left&display=inline&height=557&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1114&originWidth=1848&size=1038308&status=done&style=none&width=924)<br />

<a name="2XHur"></a>
#### 
<a name="9nOdQ"></a>
#### Codable坑点分析

<br />上面的案例中，我们在课程的刚开始讲过一个案例，那就是继承。那么如果我们把案例修改一下：
```swift
class LGPerson: Codable {
    var name: String?
    var age: Int?
}

class LGTeacher: LGPerson {
    var subjectName: String?

}

class LGPartTimeTeacher: LGPerson{
    var partTime: Double?
}

let t = LGTeacher()
t.age = 10
t.name = "Kody"
t.subjectName = "Swift"

let encoder = JSONEncoder()
let encoderData = try encoder.encode(t)
print(String(data: encoderData, encoding: .utf8))
```
我们先来看一下上面这个案例能否正常编码成功<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609137504010-2e4c0c8f-231e-4c6e-8848-01ea1ba8e0b5.png#align=left&display=inline&height=168&margin=%5Bobject%20Object%5D&name=image.png&originHeight=336&originWidth=1846&size=41435&status=done&style=none&width=923)<br />可以看到当前仅仅能正常编码成功我们的 `age` 和 `name` ,但是我们 `subjectName` 没法正常编码，我们来 `debug` 看一下当前是为什么？<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609137682028-8ff8816d-7b35-4844-a92a-ead404e409ca.png#align=left&display=inline&height=629&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1258&originWidth=2136&size=381053&status=done&style=none&width=1068)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609137906741-8fe8244c-a1c6-423d-bb36-f6a5e4189342.png#align=left&display=inline&height=575&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1150&originWidth=2082&size=347826&status=done&style=none&width=1041)<br />我们当前的 `type` 是 `LGTeacher` 并没有遵守 `if` 的各种分支，所以当前代码就执行到一下分支：<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609138031189-06f4c49e-8e29-4d3a-8fb1-53791ff2b315.png#align=left&display=inline&height=500&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1000&originWidth=1580&size=171738&status=done&style=none&width=790)<br />
<br />那这里的 `encode` 方法在我们遵守 `Codable` 协议之后，系统自动帮助我们实现了，我们通过 `SIL` 来看一下：<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609138930288-66d82f3e-c7df-4f79-9bd1-0fdedca75658.png#align=left&display=inline&height=656&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1312&originWidth=2208&size=667305&status=done&style=none&width=1104)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609138975596-d2e3f31e-b8b4-437f-b077-a31f289a7a8e.png#align=left&display=inline&height=515&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1030&originWidth=2022&size=341698&status=done&style=none&width=1011)<br />我们再来看一下 `LGPerson` 默认的实现，所以自然而然我们也就没办法访问到，当前的 `LGPerson` 的编码就会只对 `age` ， `name` 进行编码了。
```swift
class LGTeacher: LGPerson {
    var subjectName: String?
    
//    init(name: String, age: Int, subjectName: String) {
//        self.subjectName = subjectName
//        super.init(name: name, age: age)
//    }
//
//    required init(from decoder: Decoder) throws {
//        fatalError("init(from:) has not been implemented")
//    }
    
//    enum CodingKeys: String, CodingKey{
//        case subjectName
//    }
    
    override func encode(to encoder: Encoder) throws {
       var container = encoder.container(keyedBy: CodingKeys.self)
       try container.encode(subjectName, forKey: .subjectName)
       let superdecoder = container.superEncoder()
       try super.encode(to: superdecoder)
    }
}
```
但是这样写的话，就会存在另一个问题，因为当前的 `CodingKeys` 访问不到，所以这里我们就需要这样操作
```swift
class LGTeacher: LGPerson {
    var subjectName: String?
    
//    init(name: String, age: Int, subjectName: String) {
//        self.subjectName = subjectName
//        super.init(name: name, age: age)
//    }
//
//    required init(from decoder: Decoder) throws {
//        fatalError("init(from:) has not been implemented")
//    }
    
    enum CodingKeys: String, CodingKey{
        case subjectName
    }
    
    override func encode(to encoder: Encoder) throws {
       var container = encoder.container(keyedBy: CodingKeys.self)
       try container.encode(subjectName, forKey: .subjectName)
       let superdecoder = container.superEncoder()
       try super.encode(to: superdecoder)
    }
}
```
这样我们就能正确编码了<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609140202553-52a37679-6cae-4d99-89e8-772d9b556c52.png#align=left&display=inline&height=132&margin=%5Bobject%20Object%5D&name=image.png&originHeight=264&originWidth=2264&size=47959&status=done&style=none&width=1132)<br />如果我们再把代码修改一下：
```swift
class LGPerson: Codable {
    var name: String?
    var age: Int?
    
    init(name: String, age: Int) {
        self.age = age
        self.name = name
    }
}

class LGTeacher: LGPerson {
    var subjectName: String?
    
    init(name: String, age: Int, subjectName: String) {
        self.subjectName = subjectName
        super.init(name: name, age: age)
    }
    
    required init(from decoder: Decoder) throws {
        fatalError("init(from:) has not been implemented")
    }
    
    enum CodingKeys: String, CodingKey{
        case subjectName
    }
    
    override func encode(to encoder: Encoder) throws {
       var container = encoder.container(keyedBy: CodingKeys.self)
       try container.encode(subjectName, forKey: .subjectName)
       let superdecoder = container.superEncoder()
       try super.encode(to: superdecoder)
    }
}

class LGPartTimeTeacher: LGPerson{
    var partTime: Double?
}

let t: LGPerson = LGTeacher.init(name: "Kody", age: 10, subjectName: "Swift")

let encoder = JSONEncoder()
let encoderData = try encoder.encode(t)

print(String(data: encoderData, encoding: .utf8))

let t1: LGPerson = try JSONDecoder().decode(LGTeacher.self, from: encoderData)
```
可以看到这里，就直接报错了：<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609140501942-3e2ddcad-a968-40a9-9c7c-19cecf7e9454.png#align=left&display=inline&height=578&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1156&originWidth=1836&size=260818&status=done&style=none&width=918)<br />看这里好像是因为我们没有实现这个 `decoder` 的方法，那我们来实现一下：
```swift
  required init(from decoder: Decoder) throws {
//        fatalError("init(from:) has not been implemented")
        let container = try decoder.container(keyedBy: CodingKeys.self)
        
        self.subjectName = try container.decode(String.self, forKey: .subjectName)
        
        try super.init(from: decoder)
    }
```
```swift
let t1: LGPerson = try JSONDecoder().decode(LGTeacher.self, from: encoderData)
print(t1.age)
print(t1.name)
```
![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609140898636-737d3e8d-632c-45d9-9b1f-c80d73e5b05d.png#align=left&display=inline&height=171&margin=%5Bobject%20Object%5D&name=image.png&originHeight=342&originWidth=2110&size=55763&status=done&style=none&width=1055)<br />而且如果当前属性是非可选项，那么这里我们就直接会得到一个崩溃的应用程序。<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609141020258-7d60986e-a9f2-4358-adbf-071fda3d3236.png#align=left&display=inline&height=367&margin=%5Bobject%20Object%5D&name=image.png&originHeight=734&originWidth=1890&size=256158&status=done&style=none&width=945)<br />
<br />如果这里我们换成结构体，那么结果是不是一样哪？我们先来测试一下：
```swift
protocol LGPerson: Codable {
    var age: String { get set }
    var name: String { get set }
}

struct LGTeacher: LGPerson {
    var age: String
    var name: String
}

struct LGParTimeTeacher: LGPerson {
    var age: String
    var name: String
}

struct Company: Codable{
    var person: [LGPerson]
    var companyName: String
    
    enum CodingKeys: String, CodingKey {
        case person
        case companyName
    }

    func encode(to encoder: Encoder) throws {
        var container = encoder.container(keyedBy: CodingKeys.self)
        try container.encode(person, forKey: .person)
        try container.encode(companyName, forKey: .companyName)
    }
    
}
```
当前编译器就直接报错了，因为当前 `LGPerson` 是个协议，他不能遵守他自身<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609142017959-c73f70f7-c68e-4906-aabe-ac1bc89199e3.png#align=left&display=inline&height=463&margin=%5Bobject%20Object%5D&name=image.png&originHeight=926&originWidth=1820&size=211974&status=done&style=none&width=910)<br />那么怎么办那？这个时候我们可能想到的是直接在 `LGTeacher` , `LGPartTimeTeacher` 中实现 `decode 和 encode` 的方法，那我们可以找一个中间层来解决这个问题：
```swift
protocol LGPerson{
    var age: String { get set }
    var name: String { get set }
}

struct LGTeacher: LGPerson {
    var age: String
    var name: String
}

struct LGParTimeTeacher: LGPerson {
    var age: String
    var name: String
}

struct LGPersonBox : LGPerson, Codable {
    
    var age: String
    var name: String

    init(_ person: LGPerson) {
        self.age = person.age
        self.name = person.name
    }
}

struct Company: Codable{
    var person: [LGPersonBox]
    var companyName: String
}
```
```swift
protocol LGPerson{
    var age: Int { get set }
    var name: String { get set }
}

struct LGTeacher: LGPerson {
    var age: Int
    var name: String
}

struct LGParTimeTeacher: LGPerson {
    var age: Int
    var name: String
}

struct LGPersonBox : LGPerson, Codable {
    
    var age: Int
    var name: String

    init(_ person: LGPerson) {
        self.age = person.age
        self.name = person.name
    }
}

struct Company: Codable{
    var person: [LGPersonBox]
    var companyName: String
}

let person: [LGPerson] = [LGTeacher(age: 20, name: "Kody"),LGParTimeTeacher(age: 30, name: "Hank")]

let company = Company(person: person.map(LGPersonBox.init), companyName: "Logic")

let jsonEncoder = JSONEncoder()
jsonEncoder.outputFormatting = .prettyPrinted

let jsonData = try jsonEncoder.encode(company)

if let jsonString = String(data: jsonData, encoding: .utf8) {
    print(jsonString)
}
```
![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609142636124-b41a0600-98ac-4072-91bd-ef67b71e1f44.png#align=left&display=inline&height=402&margin=%5Bobject%20Object%5D&name=image.png&originHeight=804&originWidth=1310&size=80697&status=done&style=none&width=655)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609142702844-480ad752-58e6-426d-954b-f63963dd0cb8.png#align=left&display=inline&height=375&margin=%5Bobject%20Object%5D&name=image.png&originHeight=750&originWidth=2558&size=139104&status=done&style=none&width=1279)<br />
<br />可以看到这里输出的格式都是 `LGPersonBox` ,如果我们想正确的还原我们当前的类型信息，应该做什么哪？很简单的一种做法就是需要在编码过程中将我们的类型信息编码进去，什么意思那？我们通过代码来看一下：
```swift
enum LGPersonType:String, Codable {
    case teacher
    case partTeacher

    var metdadata: LGPerson.Type {
        switch self {
        case .teacher:
            return LGTeacher.self
        case .partTeacher:
            return LGParTimeTeacher.self
        }
    }
}

protocol LGPerson: Codable{
    static var type: LGPersonType{ get }
    var age: Int { get set }
    var name: String { get set }
}

struct LGTeacher: LGPerson {
    static var type: LGPersonType = LGPersonType.teacher
    var age: Int
    var name: String
}

struct LGParTimeTeacher: LGPerson {
    static var type: LGPersonType = LGPersonType.partTeacher
    var age: Int
    var name: String
}

struct LGPersonBox : Codable {

    var p: LGPerson

    init(_ p: LGPerson) {
        self.p = p
    }

    private enum CodingKeys : CodingKey {
        case type
        case p
    }

    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)

        let type = try container.decode(LGPersonType.self, forKey: .type)
        self.p = try type.metdadata.init(from: container.superDecoder(forKey: .p))
    }

    func encode(to encoder: Encoder) throws {
        var container = encoder.container(keyedBy: CodingKeys.self)

        try container.encode(type(of: p).type, forKey: .type)
        try p.encode(to: container.superEncoder(forKey: .p))
    }
}

struct Company: Codable{
    var person: [LGPersonBox]
    var companyName: String
}

let person: [LGPerson] = [LGTeacher(age: 20, name: "Kody"),LGParTimeTeacher(age: 30, name: "Hank")]

let company = Company(person: person.map(LGPersonBox.init), companyName: "Logic")

let jsonEncoder = JSONEncoder()
jsonEncoder.outputFormatting = .prettyPrinted

let jsonData = try jsonEncoder.encode(company)

if let jsonString = String(data: jsonData, encoding: .utf8) {
    print(jsonString)
}
```
当然如果我们想输出下面这个<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609145233906-91a184d4-ca87-4568-b877-0862c6dee166.png#align=left&display=inline&height=356&margin=%5Bobject%20Object%5D&name=image.png&originHeight=712&originWidth=1072&size=58193&status=done&style=none&width=536)<br />![image.png](https://cdn.nlark.com/yuque/0/2020/png/2977480/1609145249826-fe5d9901-ece6-44c3-a872-580f2024c6e3.png#align=left&display=inline&height=383&margin=%5Bobject%20Object%5D&name=image.png&originHeight=766&originWidth=2078&size=171073&status=done&style=none&width=1039)<br />
<br />新的实现方式
```swift
protocol Meta: Codable {
    associatedtype Element
    
    static func metatype(for typeString: String) -> Self
    var type: Decodable.Type { get }
}

struct MetaObject<M: Meta>: Codable {
    let object: M.Element
    
    init(_ object: M.Element) {
        self.object = object
    }
    
    enum CodingKeys: String, CodingKey {
        case metatype
        case object
    }
    
    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        let typeStr = try container.decode(String.self, forKey: .metatype)
        let metatype = M.metatype(for: typeStr)
        
        let superDecoder = try container.superDecoder(forKey: .object)
        let obj = try metatype.type.init(from: superDecoder)
        guard let element = obj as? M.Element else {
            fatalError()
        }
        self.object = element
    }
    
    func encode(to encoder: Encoder) throws {
        var container = encoder.container(keyedBy: CodingKeys.self)
        let typeStr = String(describing: type(of: object))
        try container.encode(typeStr, forKey: .metatype)
        
        let superEncoder = container.superEncoder(forKey: .object)
        let encodable = object as? Encodable
        try encodable?.encode(to: superEncoder)
    }
}

enum LGPersonType: String, Meta {
    typealias Element = LGPerson
    
    case teacher = "LGTeacher"
    case partTimeTeacher = "LGPartTimeTeacher"
    
    static func metatype(for typeString: String) -> LGPersonType {
        guard let metatype = self.init(rawValue: typeString) else {
            fatalError()
        }
        return metatype
    }
    
    var type: Decodable.Type {
        switch self {
        case .teacher:
            return LGTeacher.self
        case .partTimeTeacher:
            return LGPartTimeTeacher.self
        }
    }
}

class LGPerson: Codable {
    var name: String
    var age: Int
    
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
}

class LGTeacher: LGPerson {
    var subjectName: String
    
    init(name: String, age: Int, subjectName: String) {
        self.subjectName = subjectName
        super.init(name: name, age: age)
    }
    
    required init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        subjectName = try container.decode(String.self, forKey: .subjectName)
        
        let superDecoder = try container.superDecoder()
        try super.init(from: superDecoder)
    }
    
    override func encode(to encoder: Encoder) throws {
        var container = encoder.container(keyedBy: CodingKeys.self)
        try container.encode(subjectName, forKey: .subjectName)
        
        let superdecoder = container.superEncoder()
        try super.encode(to: superdecoder)
    }
    
    enum CodingKeys: String, CodingKey {
        case subjectName
    }
}

class LGPartTimeTeacher: LGPerson {
    var partTime: Double
    
    init(name: String, age: Int, partTime: Double) {
        self.partTime = partTime
        super.init(name: name, age: age)
    }
    
    required init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        partTime = try container.decode(Double.self, forKey: .partTime)
    
        let superDecoder = try container.superDecoder()
        try super.init(from: superDecoder)
    }
    
    override func encode(to encoder: Encoder) throws {
        var container = encoder.container(keyedBy: CodingKeys.self)
        try container.encode(partTime, forKey: .partTime)
        
        let superdecoder = container.superEncoder()
        try super.encode(to: superdecoder)
    }
    
    enum CodingKeys: String, CodingKey {
        case partTime
    }
}


let p: LGPerson = LGTeacher(name: "Kody", age: 20, subjectName: "Swift")
let jsonData = try JSONEncoder().encode(MetaObject<LGPersonType>(p))
if let str = String(data: jsonData, encoding: .utf8) {
    print(str)
}

let decode: MetaObject<LGPersonType> = try JSONDecoder().decode(MetaObject<LGPersonType>.self, from: jsonData)
```

