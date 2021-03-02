## Java代码获取本机IP地址

### 方式1
```
import java.net.InetAddress;
import java.net.UnknownHostException;

    @Override
    public String convert(ILoggingEvent event) {
        try {
            return InetAddress.getLocalHost().getHostAddress();
        } catch (UnknownHostException e) {
            LOGGER.error("获取日志Ip异常", e);
        }
        return null;
    }
```

### 方式2
```
import java.net.InetAddress;
import java.net.NetworkInterface;
import java.net.SocketException;
import java.util.Enumeration;

    private static InetAddress getLocalAddress() {
        try {
            for (Enumeration<NetworkInterface> interfaces = NetworkInterface.getNetworkInterfaces(); interfaces.hasMoreElements();) {
                NetworkInterface ni = interfaces.nextElement();
                if (ni.isLoopback() || ni.isVirtual() || !ni.isUp()) {
                    continue;
                }
                InetAddress ia = getPreferAddress(ni.getInetAddresses());
                if (ia != null) {
                    return ia;
                }
            }
        } catch (SocketException e) {
            throw new RuntimeException("获取日志Ip异常", e);
        }
        return null;
    }

    private static InetAddress getPreferAddress(Enumeration<InetAddress> enums) {
        while (enums != null && enums.hasMoreElements()) {
            InetAddress ia = enums.nextElement();
            if (ia.isLoopbackAddress() || ia instanceof Inet6Address) {
                continue;
            }
            return ia;
        }
        return null;
    }
```
