# 异常

* Throwable
  * Error
    * AWTError
    * IOError
    * LinkageError
    * ThreadDeath
    * VirtualMachineError
      * OutOfMemoryError
      * StackOverflowError
  * Exception
    * SQLException
    * IOException
    * RuntimeException
      * NullPointerException
      * ClassCastException
      * IndexOutOfBoundsException

## 异常体系

Java 的异常被分成两大类：Checked 和 Runtime 异常（运行时异常）。所有的 RuntimeException 类及其子类的实例被称为 Runtime 异常；不是 RuntimeException 类及其子类的异常实例则被称为 Checked 异常。
