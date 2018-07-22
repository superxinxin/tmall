# tmall
# 天猫商城项目
该项目仿造天猫商城搭建了一个网上商城，主要从以下几个方面介绍该项目：（1）需求分析，（2）表结构分析，（3）实体类设计，（4）DAO类设计，（5）业务
类设计，（6）后台设计之分类管理，（7）后台设计之其他管理，（8）前台设计之首页设计，（9）前台设计之无需登录状态，（10）前台设计之需要登录状态
## （1）需求分析
	1.前端页面：
		（1）商场首页
			横向导航栏上提供4个分类的链接（展示）
			在纵向导航栏上提供全部17个分类连接（展示）
			当鼠标移动到某一个纵向分类连接的时候，显示这个分类下的推荐商品（展示）
			按照每种分类，显示5个商品的方式显示所有17种分类（展示）
		（2）分类页
			显示分辨率为950x100的当前分类图片（展示）
			显示本分类下的所有产品（展示）
			分类页排序（交互）
		（3）产品页
			显示分辨率为950x100的当前商品对应的分类图片（展示）
			显示本商品的5个单独图片（展示）
			商品的基本信息，如标题，小标题，加个，销量，评价数量，库存等（展示）
			商品详情（展示）
			评价信息（展示）
			5张商品详细图片（展示）
			立即购买（交互）
			加入购物车（交互）
		（4）搜索结果页
			显示满足查询条件的商品（展示）
		（5）购物车查询页
			在购物车中显示订单项（展示）
			调整订单项数量（交互）
			删除订单项（交互）
		（6）结算页
			在结算页面显示被选中的订单项（展示）
			生成订单（交互）
		（7）确认支付页
			确认支付页面显示本次订单的金额总数（展示）
			确认付款（交互）
		（8）支付成功页
			付款成功时，显示本次付款金额（展示）
		（9）我的订单页
			显示所有订单，以及对应的订单项（展示）
		（10）确认收货页
			显示订单项内容（展示）
			显示订单信息，收货人地址等（展示）
			确认收货（交互）
		（11）评价页
			显示要评价的商品信息，商品当前的总评价数（展示）
			评价成功后，显示当前商品所有的评价信息（展示）
			提交评价信息（展示）
		（12）页头信息展示页
			未登录状态（展示）
			已登录状态（展示）
			登录（交互）
			注册（交互）
			退出（交互）
		（13）所有页面
			搜索（交互）
	2.后台页面：
		（1）分类管理
			分页查询
			新增分类
			编辑修改
			删除
		（2）属性管理
			分页查询
			新增属性
			编辑修改
			删除
		（3）产品管理
			分页查询
			新增产品
			编辑修改
			删除
		（4）产品图片管理
			新增单个图片
			新增详情图片
		（5）产品属性管理
			设置产品属性值
		（6）用户管理
			分页查询
		（7）订单管理
			分页查询
			查看详情
			发货
## （2）表结构设计
一张图表示：![表关系图](https://github.com/superxinxin/tmall/blob/master/web/img2/%E8%A1%A8%E5%85%B3%E7%B3%BB%E5%9B%BE.png)
## （3）实体类设计
	User、Category、Property、Product、ProductImage、PropertyValue、Review、Order、OrderItem
## （4）DAO设计
	工具类、UserDAO、CategoryDAO、PropertyDAO、ProductDAO、ProductImageDAO、PropertyValueDAO、ReviewDAO、OrderDAO、OrderItemDAO
	解释一下工具类：包括DBUtil和DateUtil。
	DBUtil是数据库工具类，这个类的作用是初始化驱动，并且提供一个getConnection用于获取连接。
	在后续的所有DAO中，当需要获取连接的时候，都采用这种方式进行。
	数据库连接的参数，如数据库名称，账号密码，编码方式等都设计在属性上，便于统一修改，降低维护成本。
	DateUtil是日期工具类，主要是用于java.util.Date类与java.sql.Timestamp 类的互相转换。
	因为在实体类中日期类型的属性，使用的都是java.util.Date类。而为了在MySQL中的日期格式里保存时间信息，
	必须使用datetime类型的字段，而jdbc要获取datetime类型字段的信息，需要采用java.sql.Timestamp来获取，
	否则只会保留日期信息，而丢失时间信息。
## （5）业务类
	作为J2EE web 应用，一般会按照Servlet -> Service（业务类） -> DAO -> database的设计流程进行。
	当浏览器提交请求到tomcat web服务器的时候，对应的servlet的doGet/doPost方法会被调用，接着在servlet中调用Service类，
	然后在Service类中调用DAO类，最后在DAO中访问数据库获取相应的数据。
	但是在本项目，没有使用Service这一层。原因是在DAO进行了比较详细的设计，已经提供了很好的支持业务的方法。
## （6）后台设计之分类管理
	1.存在问题：在cart项目里，一个路径对用一个Servlet，项目小，没毛病。
	但是在这里，仅分类管理就需要增加，删除，编辑，修改，查询5个服务端功能，而分类，产品，属性，产品图片，用户，订单等等
	差不多都有这些增删改查功能，导致Servlet很多，web.xml配置文件就会臃肿，并且容易出错。
	2.解决办法：以分类管理为例，用CategoryServlet一个类实现对分类的增删改查的功能，使用BackServletFilter来实现这个功能。
		（1）首先在web.xml配置文件中，让所有的请求都会经过BackServletFilter
		（2）假设访问的路径是：http: //127.0.0.1:8080/tmall/admin_category_list
		（3）在BackServletFilter中通过request.getRequestURI()取出访问的uri: /tmall/admin_category_list
		（4）然后截掉/tmall，得到路径/admin_category_list
		（5）判断其是否以/admin开头
		（6）如果是，那么就取出两个_之间的字符串，category，并且拼接成/categoryServlet，
		通过服务端跳转到/categoryServlet
		（7）在跳转之前，还取出了list字符串，然后通过request.setAttribute的方式，借助服务端跳转，
		传递到categoryServlet里去
	3.从BackServletFilter跳到CategoryServlet后，在CategoryServlet中，实现以下流程：
		（1）首先CategoryServlet继承了BaseBackServlet，而BaseBackServlet又继承了HttpServlet
		（2）服务端跳转过来之后，会访问CategoryServlet的doGet()或者doPost()方法
		（3）在访问doGet()或者doPost()之前，会访问service()方法
		（4）BaseBackServlet中重写了service() 方法，所以流程就进入到了service()中
		（5）在service()方法中有三块内容
			1）第一块是获取分页信息
			2）第二块是根据反射访问对应的方法
			3）第三块是根据对应方法的返回值，进行服务端跳转、客户端跳转、或者直接输出字符串。
		（6）根据以下方式反射访问对应的方法
			1）取到从BackServletFilter中request.setAttribute()传递过来的值 list
			2）根据这个值list，借助反射机制调用CategoryServlet类中的list()方法
		（7）这样就达到了CategoryServlet.list()方法被调用的效果
	4.通过这样一种模式，一个Servlet类就能满足CRUD一系列业务要求。
		如果访问的路径是admin_category_list，就会调用categoryServlet.list()方法
		如果访问的路径是admin_category_add，就会调用categoryServlet.add()方法
		如果访问的路径是admin_category_delete，就会调用categoryServlet.delete()方法
		如果访问的路径是admin_category_edit，就会调用categoryServlet.edit()方法
		如果访问的路径是admin_category_update，就会调用categoryServlet.update()方法
	5.JSP设计：listCategory.jsp用到了4个公共包含文件
		```
		<%@include file="../include/admin/adminHeader.jsp"%>（展示头部信息）
		<%@include file="../include/admin/adminNavigator.jsp"%>（展示导航页）
		<%@include file="../include/admin/adminPage.jsp"%>（用于分页展示）
		<%@include file="../include/admin/adminFooter.jsp"%>（展示页脚信息）
		```
		listCategory.jsp作为视图，担当的角色是显示数据。
		通过categoryDAO取得数据集合cs；
		通过request.setAttribute放在“thecs"这个key中，为后续服务端跳转到jsp之后使用；
		通过return "admin/listCategory.jsp"; 服务端跳转到视图listCategory.jsp页面，呈现所有分类数据。
## （7）后台设计之其他管理
	主要分为属性管理、产品管理、产品图片管理、产品属性值管理、用户管理、订单管理。设计思路基本与分类管理思路类似，
	故综合为其他管理，以作简要讲述	
	1.属性管理：该属性是某分类的属性，如冰箱分类有能耗、尺寸、品牌等属性，服装分类有面料、尺码、品牌等属性。
	与分类管理的设计类似，对某一分类的属性也有增删改查四个动作，对应的是PropertyServlet里的不同方法。
	2.产品管理：与分类管理的设计类似，对产品也有增删改查四个动作，对应的是ProductServlet里的不同方法。
	3.产品图片管理：对某产品的图片，只有增删查功能，对应的是ProductImageServlet里的不同方法。
	4.产品属性值管理：对某产品的多个属性值，属性是该产品所属的分类决定的，属性值只有修改编辑功能，
	直接写在了ProductServlet里，对应的是ProductServlet里的editPropertyValue方法和updatePropertyValue方法。
	5.用户管理：后台对用户的管理只需要展示出来即可，对应UserServlet里的list方法。
	6.订单管理：后台对订单管理的展示功能对应OrderServlet的list方法，此外，还有delivery方法，
	表示后台对该订单已发货，修改订单状态以呈现给前端用户。
## （8）前台设计之首页设计
	1.存在问题：与后台管理类似的，前台也存在按照传统方式一个路径对应一个Servlet的冗长的web.xml配置
	以及大量servlet类的问题。与后台管理同样的，采用Filter配合Servlet的设计模式来大大简化这么一个配置问题。
	2.解决方法：类似分类管理的设计，结合ForeServletFilter和ForeServlet来解决这个问题。
		（1）首先在web.xml配置文件中，让所有的请求都会经过ForeServletFilter
		（2）还是假设访问的路径是：http: //127.0.0.1:8080/tmall/forehome
		（3）在ForeServletFilter 中通过request.getRequestURI()取出访问的uri: /tmall/forehome
		（4）然后截掉/tmall，得到路径/forehome
		（5）判断其是否以/fore开头,并且不是/foreServlet开头
		（6）如果是，取出fore之后的值home，并且服务端跳转到foreServlet
		（7）在跳转之前，还取出了home字符串，然后通过request.setAttribute的方式，借助服务端跳转，
		传递到foreServlet里去
	3.接着，服务端跳转/foreServlet，就到了ForeServlet这个类里，在ForeServlet中，实现以下流程：
		（1）首先ForeServlet继承了BaseForeServlet，而BaseForeServlet又继承了HttpServlet
		（2）服务端跳转过来之后，会访问ForeServlet的doGet()或者doPost()方法
		（3）在访问doGet()或者doPost()之前，会访问service()方法
		（4）BaseForeServlet中重写了service() 方法，所以流程就进入到了service()中
		（5）在service()方法中有三块内容
			1）第一块是获取分页信息
			2）第二块是根据反射访问对应的方法
			3）第三块是根据对应方法的返回值，进行服务端跳转、客户端跳转、或者直接输出字符串
		（6）第一块：分页信息实际上在前台并没有用到，前台并没有提供分页功能
		（7）第二块：取到从ForeServletFilter中request.setAttribute()传递过来的值 home，
		根据这个值home，借助反射机制调用ForeServlet类中的home()方法，
		这样就达到了ForeServlet.home()方法被调用的效果
		（8）第三块： 判断根据home的返回值"home.jsp"，既没有"%"开头，也没有"@",那么就进行服务端跳转到 "home.jsp" 页面
	4.通过这样一种模式，一个Servlet类就能满足CRUD一系列业务要求。
		如果访问的路径是forecategory，就会调用foreServlet.category()方法
		如果访问的路径是forecart，就会调用foreServlet.cart()方法
		如果访问的路径是foresearch，就会调用foreServlet.search()方法
		...................
	5.JSP设计：home.jsp用到了4个公共包含文件
		```
		<%@include file="include/header.jsp"%>（展示头部信息）
		<%@include file="include/top.jsp"%>（展示导航页面）
		<%@include file="include/search.jsp"%>（展示搜索框）
		<%@include file="include/home/homePage.jsp"%>（展示主页）
		<%@include file="include/footer.jsp"%>  （展示页脚信息）
		```
## （9）前台设计之无需登录状态
	前台页面主要分为两类：无需登录和需要登录的页面。在无需登录的页面中，
	有一些功能，无需登录也可以使用的，
	比如登录，注册，分类页面，查询，产品页面等。在需要登录的页面中，
	有一些功能，必须建立在已经登录的基础之上，
	比如购买，加入购物车，查看购物车，结算页面，订单页面等等。
	无需登录页面：主要分为注册、登录、退出、产品页、模态登录、分类页、搜索
	1.注册：register.jsp
		（1）与首页的home.jsp相仿，register.jsp也包含了header.jsp,top.jsp,footer.jsp等公共页面。
		不同的是，没有使用首页的search.jsp,而是用的简单一点的simpleSearch.jsp。
		（2）注册页面registerPage.jsp的主体功能，用于提交账号密码。在提交之前会进行为空验证，
		以及密码是否一致验证。
		（3）registerPage.jsp的form提交数据到路径foreregister,
		导致ForeServlet.register()方法被调用，在这个方法中：
			1）获取账号密码；
			2）通过HtmlUtils.htmlEscape(name);把账号里的特殊符号进行转义；
			3）判断用户名是否存在，
			如果已经存在，就服务端跳转到reigster.jsp，并且带错误提示信息；
			如果不存在，则加入到数据库中，并服务端跳转到registerSuccess.jsp页面。
	2.登录:login.jsp
		（1）与register.jsp相仿，login.jsp也包含了header.jsp,footer.jsp等公共页面。
		登录业务页面是loginPage.jsp。
		（2）注册业务页面loginPage.jsp用于向服务器提交账号和密码，
		loginPage.jsp的form提交数据到路径forelogin,导致ForeServlet.login()方法被调用，
		在这个方法中：
			1）获取账号密码	；
			2）把账号通过HtmlUtils.htmlEscape进行转义；
			3）根据账号和密码获取User对象，
			如果对象为空，则服务端跳转回login.jsp，带上错误信息，
			并且使用login.jsp中的办法显示错误信息；
			如果对象存在，则把对象保存在session中，
			并客户端跳转到首页"@forehome"。
	3.退出： 在ForeServlet的logout()方法里，将用户对象从session中移除。
	4.产品页：product.jsp
		（1）点击某产品，通过访问地址http: //127.0.0.1:8080/tmall/foreproduct?pid=844。
		导致ForeServlet.product()方法被调用，在这个方法中：
			1）获取参数pid；
			2）根据pid获取Product对象p；
			3）根据对象p，获取这个产品对应的单个图片集合；
			4）根据对象p，获取这个产品对应的详情图片集合；
			5）获取产品的所有属性值；
			6）获取产品对应的所有的评价；
			7）设置产品的销量和评价数量；
			8）把上述取值放在request属性上；
			9）服务端跳转到 "product.jsp" 页面。product.jsp也包含了header.jsp,top.jsp,
			simpleSearch.jsp，footer.jsp等公共页面。
			产品业务页面是productPage.jsp。productPage.jsp由3个页面组成：
			imgAndInfo.jsp（单个图片和基本信息），productReview.jsp（评价信息），productDetail.jsp（详情图片）。
	5.模态登录：
		（1）在未登录状态，点击购买或者加入购物车就会弹出模态对话框，
		在这个模态对话框上可以进行登录操作，如果账号和密码出错会出现"账号密码错误"提示。
		（2）立即购买和加入购物车这两个按钮的监听是放在imgAndInfo.jsp页面中，
		这两个按钮都会通过JQuery的.get方法，用异步ajax的方式访问forecheckLogin，获取当前是否登录状态，
		如果返回的不是"success" 即表明是未登录状态，那么就会打开登录的模态窗口。
		（3）在ajax访问路径/forecheckLogin，导致ForeServlet.checkLogin()方法被调用。
		在方法里，获取session中的"user"对象，
		如果不为空，即表示已经登录，返回字符串"success"，
		如果为空，即表示未登录，返回字符串"fail"。
		（4）modal.jsp模态登录页面被footer.jsp所包含，所以每个页面都是加载了的。
		通过imgAndInfo.jsp页面中的购买按钮或者加入购物车按钮显示出来。
		（5）点击登录按钮时，使用imgAndInfo.jsp页面中的ajax代码进行登录验证，即点击了登录按钮之后，
		访问路径/foreloginAjax,导致ForeServlet.loginAjax()方法被调用，在方法里：
			1）获取账号密码，
			2）通过账号密码从数据库获取User对象，
			如果User对象为空，那么就返回"fail"字符串；
			如果User对象不为空，那么就把User对象放在session中，并返回"success"字符串。
	6.分类页面：category.jsp
		（1）分类这个页面对产品有排序功能，所以先准备了5个Comparator比较器：
			1）ProductAllComparator综合比较器，把销量乘以评价高的放前面；
			2）ProductReviewComparator人气比较器，把评价数量多的放前面；
			3）ProductDateComparator新品比较器，把创建日期晚的放前面；
			4）ProductSaleCountComparator销量比较器，把销量高的放前面；
			5）ProductPriceComparator价格比较器，把价格低的放前面。
		（2）在ForeServlet.category()方法里：
			1）获取参数cid；
			2）根据cid获取分类Category对象c；
			3）为c填充产品；
			4）为产品填充销量和评价数据；
			5）获取参数sort；
			如果sort==null，即不排序；
			如果sort!=null，则根据sort的值，从5个Comparator比较器中选择一个对应的排序器进行排序；
			6）把c放在request中；
			7）服务端跳转到category.jsp。与register.jsp相仿，category.jsp也包含了
			header.jsp,top.jsp,simpleSearch.jsp，footer.jsp等公共页面，还有分类业务页面categoryPage.jsp。
		（3）categoryPage.jsp里有3个内容：
			1）显示当前分类图片；
			2）排序条sortBar.jsp，可根据综合、人气、新品、销量、价格排序。；
			3）产品列表productsByCategory.jsp，展示当前分类下的所有产品，
			通过forEach遍历c.products集合里的每个产品，并把产品标题，价格，图片，评价数，成交数打印出来。
	7.搜索：search.jsp以及simpleSearch.jsp 
		（1）每个页面都包含了搜索的jsp，首页和搜索结果页包含的是search.jsp，其他页面包含的是simpleSearch.jsp。
		这两个页面都提供了一个form，提交数据keyword到foresearch这个路径。
		（2）通过search.jsp或者simpleSearch.jsp提交数据到路径/foresearch，
		导致ForeServlet.search()方法被调用，在这个方法中：
			1）获取参数keyword；
			2）根据keyword进行模糊查询，获取满足条件的前20个产品；
			3）为这些产品设置销量和评价数量；
			4）把产品设置在request的"ps"属性上；
			5）服务端跳转到 searchResult.jsp 页面。
		（3）与register.jsp相仿，searchResult.jsp也包含了header.jsp,top.jsp,search.jsp，footer.jsp等公共页面。
		搜索结果业务页面是searchResultPage.jsp，
		searchResultPage.jsp直接包含了productsBySearch.jsp，
		productsBySearch.jsp显示查询结果：
			1）遍历ps，把每个产品的图片，价格，标题等信息显示出来；
			2）如果ps为空，则显示 "没有满足条件的产品"。
## （10）前台设计之需要登录状态
	需要登录的前台功能都是和购买支付流程紧密相关的，所以先对购买流程及其对应的MySQL表关系进行简单介绍：
	1.购买的业务流程：1）登录；2）访问产品页；3）立即购买；4）进入结算页面；5）加入购物车；6）查看购物车；
	7）选中购物车中的商品；8）又到了第4步的结算页面；9）在结算页面生成订单；10）付款；11）确认收货；12）评价；
	一波图。。。
	2.购物流程环节与表关系：围绕购物流程最重要的两个表是OrderItem和Order表，对应关系如下：
		（1）立即购买 —— 新增OrderItem
		（2）加入购物车 —— 新增OrderItem
		（3）查看购物车 —— 显示未和Order关联的OrderItem
		（4）选中购物车中的商品 —— 选中OrderItem
		（5）结算页面 —— 显示选中的OrderItem
		（6）生成订单 —— 新增Order
		（7）付款 —— 修改Order状态
		（8）我的订单 —— 显示Order
		（9）确认收货 —— 修改Order状态
		来一波图。。。
	3.立即购买：登录之后，点击立即购买，如访问地址http: //127.0.0.1:8080/tmall/forebuyone?pid=844&num=3，
	带上了产品id：844和购买数量num：3。
		（1）通过访问地址/forebuyone，导致ForeServlet.buyone()方法被调用。在这个方法中：
			1）获取参数pid；
			2）获取参数num；
			3）根据pid获取产品对象p；
			4）从session中获取用户对象user
		（2）接下来就是新增订单项OrderItem，新增订单项要考虑两个情况：
			1）如果已经存在这个产品对应的OrderItem，并且还没有生成订单，即还在购物车中，
			那么就应该在对应的OrderItem基础上调整数量，即：
				1)基于用户对象user，查询没有生成订单的订单项集合；
				2）遍历这个集合；
				3）如果产品是一样的话，就进行数量追加；
				4）获取这个订单项的id。
			2）如果不存在对应的OrderItem,那么就新增一个订单项OrderItem，即：
				1）生成新的订单项；
				2）设置数量，用户和产品；
				3）插入到数据库；
				4）获取这个订单项的id。
		（3）最后， 基于这个订单项id客户端跳转到结算页面/forebuy。
	4.进入结算页面：ForeServlet.buy()方法和buy.jsp
		（1）ForeServlet.buy()方法：在点立即购买后，客户端跳转到结算的路径：
		"@forebuy?oiid="+oiid;导致ForeServlet.buy()方法被调用，在方法中：
			1）通过getParameterValues获取参数oiid，这里要用getParameterValues试图获取多个oiid，
			而不是getParameter仅仅获取一个oiid。
			因为根据购物流程环节与表关系，结算页面还需要显示在购物车中选中的多条OrderItem数据，
			所以为了兼容从购物车页面跳转过来的需求（即8.购物车页面操作中的（3）提交操作），
			要用getParameterValues获取多个oiid；
			2）准备一个泛型是OrderItem的集合ois；
			3）根据前面步骤获取的oiids，从数据库中取出OrderItem对象，并放入ois集合中；
			4）累计这些ois的价格总数，赋值在total上；
			5）把订单项集合放在session的属性 "ois" 上；
			6）把总价格放在request的属性 "total" 上；
			7）服务端跳转到buy.jsp
		（2）buy.jsp设计：与register.jsp相仿，buy.jsp也包含了header.jsp,top.jsp,footer.jsp等公共页面。
		结算业务页面是buyPage.jsp。在buyPage.jsp中：
			1）遍历出订单项集合ois中的订单项数据；
			2）显示总金额
	5.加入购物车： ForeServlet.addCart方法
		（1）登录之后，点击加入购物车，会使用Ajax访问地址，
		如http: //127.0.0.1:8080/tmall/foreaddCart?pid=264&num=4，带上了产品id：264和购买数量num：4。
		（2）访问地址/foreaddCart导致ForeServlet.addCart()方法被调用。
		addCart()方法和立即购买中的ForeServlet.buyone()步骤做的事情是一样的，
		区别在于返回不一样，在这个方法中：
			1）获取参数pid；
			2）获取参数num；
			3）根据pid获取产品对象p；
			4）从session中获取用户对象user
		（3）接下来就是新增订单项OrderItem，新增订单项要考虑两个情况：
			1）如果已经存在这个产品对应的OrderItem，并且还没有生成订单，即还在购物车中。
			那么就应该在对应的OrderItem基础上，调整数量。即：
				1）基于用户对象user，查询没有生成订单的订单项集合；
				2）遍历这个集合；如果产品是一样的话，就进行数量追加；
				3）获取这个订单项的id。
			2）如果不存在对应的OrderItem,那么就新增一个订单项OrderItem。即：
				1）生成新的订单项；
				2）设置数量，用户和产品；
				3）插入到数据库；
				4）获取这个订单项的id。
		（3）与ForeServlet.buyone() 客户端跳转到结算页面不同的是，
		ForeServlet.addCart（）最后返回字符串"success"，表示添加成功。
	6.查看购物车：ForeServlet.cart()和cart.jsp
		（1）访问地址/forecart导致ForeServlet.cart()方法被调用，在这个方法中：
			1）通过session获取当前用户，已登录，才能获取用户对象；
			2）获取与这个用户关联的订单项集合ois；
			3）把ois放在request中；
			4）服务端跳转到cart.jsp
		（2）cart.jsp设计：cart.jsp包含了header.jsp,top.jsp,simpleSearch.jsp，footer.jsp等公共页面。
		购物车业务页面是cartPage.jsp。cartPage.jsp即遍历订单项集合ois。
	7.登录状态filter：
		（1）存在问题：查看购物车页面必须建立在已经登录的状态之上。
		如果没有登录，就会访问地址：http: //127.0.0.1:8080/tmall/forecart，会导致异常产生。
		（2）解决办法：准备一个过滤器，当访问那些需要登录才能做的页面的时候，进行是否登录的判断，
		如果不通过，那么就跳转到login.jsp页面去，提示用户登录。
		不需要登录也可以访问的，如：注册，登录，产品，首页，分类，查询等等。
		需要登录才能够访问的，如：购买行为，加入购物车行为，查看购物车，查看我的订单等等。
		（3）ForeAuthFilter，这个过滤器判断如果不是注册，登录，产品展示这些，就进行登录校验：
			1）准备字符串数组 noNeedAuthPage，存放那些不需要登录也能访问的路径
			2）获取uri
			3）去掉前缀/tmall
			4）如果访问的地址是/fore开头，又不是/foreServlet：
			取出fore后面的字符串，比如是forecart,那么就取出cart；
			判断cart是否是在noNeedAuthPage；
			如果不在，那么就需要进行是否登录验证；
			从session中取出"user"对象；
			如果对象不存在，就客户端跳转到login.jsp；否则就正常执行。
	8.购物车页面操作：购物车页面和服务端的交互主要是三个：（1）增加、减少某种产品的数量；
			（2）删除某种产品；（3）选中产品后，提交到结算页面
		（1）增加、减少某种产品的数量：点击增加或者减少按钮后，根据cartPage.jsp中的js代码，
		会通过Ajax访问/forechangeOrderItem路径，导致ForeServlet.changeOrderItem()方法被调用，在方法中：
			1）判断用户是否登录；
			2）获取pid和number；
			3）遍历出用户当前所有的未生成订单的OrderItem；
			4）根据pid找到匹配的OrderItem，并修改数量后更新到数据库；
			5）返回字符串"success"
		（2）删除某种产品：点击删除按钮后，根据cartPage.jsp中的js代码，会通过Ajax访问/foredeleteOrderItem路径，
		导致ForeServlet.deleteOrderItem方法被调用，在这个方法中：
			1）判断用户是否登录；
			2）获取oiid；
			3）删除oiid对应的OrderItem数据；
			4）返回字符串"success"
		（3）选中产品后，提交到结算页面：在选中了购物车中的任意商品之后，结算按钮呈现可点击状态。
		点击之后，提交到结算页面，并带上多个被选中的OrderItem对象的id，
		如地址http: //127.0.0.1:8080/tmall/forebuy?oiid=2&oiid=1，之后的流程就进入了前面步骤4的结算页面。
	9.订单状态图：
		（1）一张图：
		（2） 订单状态讲解 ：
			1）首先是创建订单，创建好之后，订单处于waitPay待付款状态；
			2）接着是付款，付款后，订单处于waitDelivery 待发货状态；
			3）前两步都是前台用户操作导致的，接下来需要到后台做发货操作，发货后，订单处于waitConfirm待确认收货状态；
			4）接着是前台用户进行确认收货操作，操作之后，订单处于waitReview待评价状态；
			5）最后进行评价，评价之后，订单处于finish完成状态
		以上状态都是一个接一个的，不能跳状态进行。无论当前订单处于哪个状态，都可以进行删除操作。
		但是，订单这样极其重要的业务数据，实际上是不运行真正从数据库中删除掉的，
		而是把状态标记为删除，以表示其被删掉了，所以在删除之后，订单处于delete已删除状态。
	10.生成订单：
		（1）首先通过立即购买或者购物车的提交到结算页面。进入结算页面，然后点击提交订单。
		提交订单后，在数据库中生成一条Order记录。此外，订单项的oid字段也会被设置为这条Order记录的id。
		（2）提交订单访问路径/forecreateOrder, 导致ForeServlet.createOrder方法被调用，在方法中：
			1）从session中获取user对象；
			2）获取地址，邮编，收货人，用户留言等信息；
			3）根据当前时间加上一个4位随机数生成订单号；
			4）根据上述参数，创建订单对象；
			5）把订单状态设置为等待支付；
			6）加入到数据库；
			7）从session中获取订单项集合 ( 在结算功能的ForeServlet.buy()中，订单项集合被放到了session中 )；
			8）遍历订单项集合，设置每个订单项的order，并更新到数据库；
			9）统计本次订单的总金额；
			10）客户端跳转到确认支付页forealipay，并带上订单id和总金额
		（3）确认支付页面alipay.jsp
			1）在上一步客户端跳转到路径/forealipay方法，导致ForeServlet.alipay()方法被调用。
			alipay()只是服务端跳转到了alipay.jsp页面。
			2）alipay.jsp与register.jsp相仿，alipay.jsp也包含了header.jsp,top.jsp，footer.jsp等公共页面。
			确认支付业务页面 是alipayPage.jsp。
			3）alipayPage.jsp显示总金额，并且让确认支付按钮跳转到页面/forepayed页面，并带上oid和金额。
		（4）支付成功页payed.jsp
			1）在上一步确认支付按钮提交数据到/forepayed,导致ForeServlet.payed方法被调用，在方法中：
				1）获取参数oid
				2）根据oid获取到订单对象order
				3）修改订单对象的状态和支付时间
				4）更新这个订单对象到数据库
				5）把这个订单对象放在request的属性"o"上
				6）服务端跳转到payed.jsp
			2）payed.jsp包含了header.jsp,top.jsp,simpleSearch.jsp，footer.jsp等公共页面。
			支付成功业务页面是payedPage.jsp
			3）payedPage.jsp显示订单中的地址，邮编，收货人，手机号码等等。
	11.我的订单页：ForeServlet.bought()和bought.jsp
		（1）ForeServlet.bought()方法：/forebought导致ForeServlet.bought()方法被调用，在这个方法中：
			1）通过session获取用户user；
			2）查询user所有的状态不是"delete" 的订单集合os；
			3）为这些订单填充订单项；
			4）把os放在request的属性"os"上；
			5）服务端跳转到bought.jsp
		（2）bought.jsp包含了header.jsp,top.jsp, simpleSearch.jsp，footer.jsp等公共页面。
		我的订单是页面boughtPage.jsp。在boughtPage.jsp中：
			1）遍历订单集合os，取出每个订单，显示其创建日期，订单号，总数量和总金额等；
			2）遍历每个订单下的订单项集合o.orderItems，显示每个订单项对应的产品的图片，标题，原始价格，优惠价格等
	12.我的订单页操作
		（1）在我的订单页面，根据订单的不同状态，可以做出如下不同的操作：
			1）付款 —— 已经生成订单但未付款状态下
			2）确认收货 —— 通过后台发货后
			3）评价 —— 确认收货后
			4）删除 —— 任意状态下
		（2）付款：点击付款按钮直接跳转到在生成订单中的确认支付页环节，已在10.生成订单介绍中。
		（3）确认收货
			1）点击确认收货后，访问地址/foreconfirmPay
			2）ForeServlet.confirmPay()方法被调用，在方法中：
				1）获取参数oid
				2）通过oid获取订单对象o
				3）为订单对象填充订单项
				4）把订单对象放在request的属性"o"上
				5）服务端跳转到 confirmPay.jsp
			3）confirmPay.jsp包含了header.jsp,top.jsp,simpleSearch.jsp，footer.jsp等公共页面。
			订单确认业务页面是confirmPayPage.jsp
			4）confirmPayPage.jsp功能：
				1）显示订单的创建时间，付款时间和发货时间，以及订单号，收款人信息等
				2）遍历订单项集合，显示其中的产品图片，产品标题，价格，数量，小计，总结信息
				3）最后提供确认支付按钮，提交到/foreorderconfirmed路径
		（4）确认收货成功
		通过上一步最后的确认支付按钮，提交到路径/foreorderConfirmed，导致ForeServlet.orderConfirmed()方法被调用
			1）ForeServlet.orderConfirmed()方法
				1）获取参数oid
				2）根据参数oid获取Order对象o
				3）修改对象o的状态为等待评价，修改其确认支付时间
				4）更新到数据库
				5）服务端跳转到orderConfirmed.jsp页面
			2）orderConfirmed.jsp包含了header.jsp,top.jsp,simpleSearch.jsp，footer.jsp等公共页面。
			确认收货成功业务页面是 orderConfirmedPage.jsp
			3）orderConfirmedPage.jsp显示"交易已经成功，卖家将收到您的货款。"
		（5）评价：点击评价按钮，跳转到路径/forereview,这部分在13.评价产品进行介绍
		（6）删除：在我的订单页上点击删除按钮，根据boughtPage.jsp中的ajax操作，
		会访问路径/foredeleteOrder，导致ForeServlet.deleteOrder方法被调用
			1）ForeServlet.deleteOrder()
				1）获取参数oid
				2）根据oid获取订单对象o
				3）修改状态
				4）更新到数据库
				5）返回字符串"success"
			2）boughtPage.jsp中的javascript代码获取返回字符串是success的时候，隐藏掉当前这行订单数据。
	13.评价产品
		（1）评价产品页面：通过点击评价按钮，来到路径/forereview，导致ForeServlet.review()方法被调用
			1）ForeServlet.review()
				1）获取参数oid
				2）根据oid获取订单对象o
				3）为订单对象填充订单项
				4）获取第一个订单项对应的产品,因为在评价页面需要显示一个产品图片，那么就使用这第一个产品的图片了
				5）获取这个产品的评价集合
				6）为产品设置评价数量和销量
				7）把产品，订单和评价集合放在request上
				8）服务端跳转到 review.jsp
			2）review.jsp包含了header.jsp,top.jsp,simpleSearch.jsp，footer.jsp等公共页面。
			产品业务页面是reviewPage.jsp
			3）在reviewPage.jsp中显示产品图片，产品标题，价格，产品销量，产品评价数量，以及订单信息等。


# 总结：
## （1）典型场景
	购物车（立即购买、加入购物车、查看购物车页面、购物车页面操作），
	订单状态流转（生成订单、确认支付、后台发货、确认收货、评价），
	CRUD（后台各种功能），
	分页（后台各种功能），
	一类产品多属性配置（属性管理），
	一款产品多图片维护（产品图片管理），
	产品展示（前台首页、前台产品页），
	搜索查询（搜索），
	登录、注册（注册、登录、退出），
	登录验证（登录状态Filter）。
## （2）设计模式
	1.MVC
		MVC设计模式贯穿于整个后台与前台功能开发始末
	2.Filter+Servlet+反射
		采用Filter+Servlet+反射的设计模式，
		把原本后台需要几十个Servlet的经典Servlet设计方式，精简到了个位数。
		把原本前台需要几十个Servlet的经典Servlet设计方式，精简到了个位数。
		web.xml的配置文件也大大减少，降低了开发和维护的工作量，减少了出错的几率。
	3.统一的分页查询简化开发
		所有的后台都使用同一个分页机制，仅仅需要一份简化的adminPage.jsp即满足了各种分页功能的需求，简化了开发，提升了开发速度。
	4.模块化JSP设计
		从大的JSP文件中，通过JSP包含关系抽象出多个公共文件，并把业务JSP按照功能，设计为多个小的JSP文件，便于维护和理解
