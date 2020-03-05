# token
token安全令牌的相关的代码以及所需要的gradle依赖


<br>
1.引入token安全令牌相关的依赖
compile group: 'com.auth0', name: 'java-jwt', version: '2.2.0' //新增--JWT   token安全令牌


<br>
2.登录成功后,生成TOKEN
```
 User user= loginMapper.getUserByPhoneAndLoginPassword(phone,password);
        if(user!=null)
        {
            String token=null;
            try
            {
                //生成token秘钥
                token= JwtUtils.encode(user,2592000000L);//过期时间
            }
            catch (IOException e)
            {
                e.printStackTrace();
            }
            user.setToken(token);
            jsonResponseResult.setData(user);
            
 ```          

<br>
3. 在访问每个控制层方法时,需要在拦截器中校验TOKEN合法性
```
String token= request.getParameter("token");
        //检测token是否为空
        if(token==null) {
            response.sendRedirect(request.getContextPath()+"/api/login/tokenException");
            return false;
        }
        else
        {   //token不为空检查是否过期或不正确
            try
            {
                User user= JwtUtils.decode(token,User.class);
                if(user==null)
                {
                   throw new RuntimeException("token验证错误");
                    response.sendRedirect(request.getContextPath()+"/api/login/tokenException");
                    return false;
                }

            }
            catch (Exception e)
            {
                response.sendRedirect(request.getContextPath()+"/api/login/tokenException");
                return false;
            }
        }
        return  true;
```
