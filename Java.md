装饰者模式代码

```
package demo01;

// 装饰者模式和普通的继承的区别,
/*
 * 可以重复使用同一个装饰者去增强被装饰者,而继承必须重写一个新类来增强
 */
public class Demo01 {
    public static void main(String[] args) {
        ABattercake aBattercake;
        aBattercake = new Battercake();
        // 比如这里,我第一次使用装饰者增强一个煎饼,如果继承也是可以这样写增强一个煎饼
        aBattercake = new EggDecorator(aBattercake);
        // 第二次装饰者又使用同样的手法去增强一个煎饼,如果继承的话要重新继承一个类去增强煎饼
        aBattercake = new EggDecorator(aBattercake);
        // 以此类推
        aBattercake = new SausageDecorator(aBattercake);
        System.out.println(aBattercake.getDesc()+" 销售价格:"+aBattercake.cost());
    }
}
/*
	煎饼 加一个鸡蛋 加一个鸡蛋 加一根香肠 销售价格:12
*/

// 抽象被装饰者
abstract class ABattercake {
    protected abstract String getDesc();
    protected abstract int cost();
}

// 具体被装饰者
class Battercake extends ABattercake {
    @Override
    protected String getDesc() {
        return "煎饼";
    }

    @Override
    protected int cost() {
        return 8;
    }
}

// 抽象装饰者
// 抽象装饰者继承抽象类以保持接口规范,也就是需要拥有被装饰者中的方法
// 包含被装饰类的引用,可以通过多态的方式对被装饰类进行装饰
class AbstractDecorator extends ABattercake {
    private ABattercake aBattercake;

    public AbstractDecorator(ABattercake aBattercake) {
        this.aBattercake = aBattercake;
    }
    @Override
    protected String getDesc() {
        return this.aBattercake.getDesc();
    }

    @Override
    protected int cost() {
        return this.aBattercake.cost();
    }
}

// 具体装饰者
class EggDecorator extends AbstractDecorator {
    public EggDecorator(ABattercake aBattercake) {
        super(aBattercake);
    }

    @Override
    protected String getDesc() {
        return super.getDesc()+" 加一个鸡蛋";
    }

    @Override
    protected int cost() {
        return super.cost()+1;
    }
}

// 具体装饰者
class SausageDecorator extends AbstractDecorator{
    public SausageDecorator(ABattercake aBattercake) {
        super(aBattercake);
    }

    @Override
    protected String getDesc() {
        return super.getDesc()+" 加一根香肠";
    }

    @Override
    protected int cost() {
        return super.cost()+2;
    }
}

```

