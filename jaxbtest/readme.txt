jdk版本是1.7。
该例子就是使用java提供的jaxb进行xml和pojo之间的转换。
主要是pojo中使用了很多注解，来和生成的xml文件对应，部分注解解释如下：


Marshal 和 UnMarshal的过程并不复杂，只需要从JAXBContext中获得Marshaller或Unmarshaller对象，就可以让JAXB帮我们 来进行转换。

1.将 xml文件中的各个节点和属性信息创建对应的Java模型

2.在Java模型中的创建与 xml 文件对应的节点和属性需要用注解来表示
@XmlRootElement   将一个Java类映射为一段XML的根节点
参数：
name  定义这个根节点的名称
    namespace   定义这个根节点命名空间

@XmlAccessorType  定义映射这个类中的何种类型需要映射到XML。可接收四个参数，分别是：
XmlAccessType.PROPERTY：映射这个类中的属性（get/set方法）到XML
XmlAccessType.PUBLIC_MEMBER：将这个类中的所有public的field或property同时映射到XML（默认）
XmlAccessType.NONE：不映射
XmlAccessType.FIELD：非静态属性，非transient属性映射到XML

@XmlElement  指定一个字段或get/set方法映射到XML的节点。如，当一个类的XmlAccessorType 被标注为PROPERTY时，在某一个没有get/set方法的字段上标注此注解，即可将该字段映射到XML。
参数：
defaultValue  指定节点默认值
name          指定节点名称
namespace     指定节点命名空间
    required     是否必须（默认为false）
    nillable     该字段是否包含 nillable="true" 属性（默认为false）
    type        定义该字段或属性的关联类型

@XmlAttribute  指定一个字段或get/set方法映射到XML的属性。
参数：
name             指定属性名称
namespace    指定属性命名空间
required         是否必须（默认为false）

@XmlTransient  定义某一字段或属性不需要被映射为XML。
如，当一个类的XmlAccessorType 被标注为PROPERTY时，在某一get/set方法的字段上标注此注解，那么该属性则不会被映射。

@XmlType  定义映射的一些相关规则
参数：
propOrder        指定映射XML时的节点顺序
factoryClass     指定UnMarshal时生成映射类实例所需的工厂类，默认为这个类本身
factoryMethod  指定工厂类的工厂方法
name               定义XML Schema中type的名称 
namespace      指定Schema中的命名空间

@XmlElementWrapper  为数组元素或集合元素定义一个父节点。
如，类中有一元素为List items，若不加此注解，该元素将被映射为
    <items>...</items>
    <items>...</items>
这种形式，此注解可将这个元素进行包装，如：
    @XmlElementWrapper(name="items")
    @XmlElement(name="item")
    public List items;
将会生成这样的XML样式：
    <items>
        <item>...</item>
        <item>...</item>
    </items>
    
    
3.示例代码
//Java Entity
@XmlAccessorType(XmlAccessType.FIELD)
@XmlRootElement(name = "automation")
public class JavaBean
{
    @XmlAttribute(name = "service")
    private String serviceName;
    
    @XmlElementWrapper(name = "rules")
    @XmlElement(name = "rule")
    private List<Rule> rules;
    
    @XmlElementWrapper(name = "actions")
    @XmlElement(name = "action")
    private List<Action> actions;
    
    public List<Rule> getRules()
    {
        return rules;
    }
    
    public String getServiceName()
    {
        return serviceName;
    }
    
    public void setServiceName(String serviceName)
    {
        this.serviceName = serviceName;
    }
    
    public void setRules(List<Rule> rules)
    {
        this.rules = rules;
    }
    
    public List<Action> getActions()
    {
        return actions;
    }
    
    public void setActions(List<Action> actions)
    {
        this.actions = actions;
    }
}
其中使用了ThreadLocal<Unmarshaller> obj = new ThreadLocal<Unmarshaller>()，网上百度了一个类似的使用，如下：
private static String ThreadLocal<Connection> t1 = new ThreadLocal<Connection>();
这个事用来做什么的？？
你可以认为ThreadLocal是一个线程局部变量。
你的这条语句的意思是：声明了一个 ThreadLocal 变量t1，t1可以为每一个引用该类的线程保存Connection类型的对象。 
 当多个线程都是用这个类时， 每个线程可以将自己的Connection对象，保存在t1中，各个线程中的Connection对象不会交叉混乱，
 当各个线程要使用自己的Connection时，只需要要调用 t1.Get()，返回的必然是当前线程保存的那个Connection。
总而言之，ThreadLocal就是 线程 局部 变量，同时它是泛型的，<>中的类型，就是局部变量的类型。使用Set方法来设置局部变量的值，
使用Get方法来获取局部变量的值。