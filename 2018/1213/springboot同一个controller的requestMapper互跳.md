#### 先讲返回值，你可以定义为你的返回值为ModelAndView 或者为String。但是必须url之前要携带redirect:
1. return new ModelAndView("redirect:/XXXXXX");
2. return new String(redirect:/XXXXXX);

#### 接下来将传参,通过RedirectAttributes attr
1. attr.addAttribute("id",id);//这个参数会携带在url后面，不安全
2. attr.addFlashAttribute("id",id);//会隐藏参数,类似post,藏在session里面，用完就刷

#### 接下来获取attr携带的参数
1. 在接口里面使用@requestParam 来获取addAttribute携带的参数
2. 在接口里面使用@ModelAttribute,来获取addFlashAttribute携带的参数