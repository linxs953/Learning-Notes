# autoflow design for ui automation

- 概要
   - 抽象多个Flow对象
   - Flow对象提供getEndPage方法，提供给其他Flow对象使用
   - Flow对象通过调用withStartPage(flow.getEndPage()).start()
   - 通过各个Flow对象结果的相互引用把整个业务流程串联起来