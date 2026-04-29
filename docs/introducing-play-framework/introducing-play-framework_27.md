# 一个展示示例主页的控制器示例

GET /bookshop controllers.Application.index

GET /bookshop/book/:id/ controllers.Application.getBook(id:String) GET /bookshop/book/search/ controllers.Application.search ByTitle(keyword:String)

GET /bookshop/book/echo controllers.Application.

echoService

GET /bookshop/book/:id/page/$page<[0-9]+>/ controllers.

Application.fetchBookpage(id:String, page:Integer)

GET /bookshop/example/largeresponse controllers.Application.

processLargeResponse()

GET /bookshop/authors/ controllers.Application.authors

(limit: Integer = 10)

GET /bookshop/showcomment/ controllers.Application.showComment (userid ?= null)

GET /bookshop/contact controllers.Application.contact()

GET /bookshop/book/top controllers.Application.topThreeBooks() POST /bookshop/book/save/comment/ controllers.Application.

saveComment(request:Request)

POST /greeting controllers.Application.acknowledgeGreeting

(request:Request)

POST /greeting/xml controllers.Application.acknowledgeGreeting XML(request:Request)

POST /greeting/xml/jaxb controllers.Application.acknowledgeGr eetingXMLJaxbVersion(request:Request)

