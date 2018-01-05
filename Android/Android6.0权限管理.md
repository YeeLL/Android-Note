# Android 动态权限管理
从Android 6.0（API级别23）开始，用户在应用运行时（而不是在安装应用时）向应用授予权限。 此方法简化了应用安装过程，因为用户在安装或更新应用时不需要授予权限。 它还给用户更多的控制应用程序的功能; 例如，用户可以选择给摄像机应用访问摄像机而不是设备位置。 用户可以随时通过转到应用的设置屏幕撤消权限。<!--more-->

### 随着Android6.0的普及，我们不得不做的一件事情就是关于运行时权限管理的事情。

## 1.什么叫运行时权限管理？
比如说，我们在app中打开相机的预览的功能的时候，我们的手机就会弹出一个提示框，提醒用户是否允许我们的app使用相机（也就是提供相机的权限）。这就相机权限就叫运行时权限，简而言之就是我们当我们需要某一项功能的时候所需要的权限。

## 2.Android6.0之后我们为什么需要管理动态权限？
就如上面所说的例子，当我们的用户不小心或者就是不想给相机权限时，我们的app就不能正常工作。所以这个时候我们就需要给用户一些友好的提示，或者最少不让app崩溃。这个时候我们就需要管理运行时权限了。


## 3.在Android中如何加入运行权限管理？[API地址](https://developer.android.com/reference/android/support/v4/content/PermissionChecker.html)  
运行时权限管理在代码中主要依靠几个类和几个方法来完成  
1.PermissionChecker类（最主要的类）  
主要提供检测权限的方法 

2.PermissionChecker.checkSelfPermission(context,permission）（主要的方法）   
用于检测当前是否有permission这个权限。                               
主要返回3个值:(是个int类型的值)   
* PERMISSION_GRANTED(0 (0x00000000)):有此权限   
* PERMISSION_DENIED（-1 (0xffffffff)）: 没有此权限  
* PERMISSION_DENIED_APP_OP （-2 (0xfffffffe)）:  权限被拒绝，因为不允许应用操作   

3.context.shouldShowRequestPermissionRationale(permission)  
这个方法主要用来获取是否应该显示具有请求权限的理由的UI。具体就是，这个方法会告诉你是否可以通过请求来显示友好的权限申请框。它会返回一个boolean值。   
＊ true：表示可以申请。
＊ false：表示不可以申请。（如果不能申请，就可以自己做一些事情）

4.requestPermissions(@NonNull String[] permissions, int requestCode)      
申请请求一组权限，此方法可以一次申请多个权限，通过OnRequestPermissionsResultCallback方法获得用户操作后的权限状态

5.OnRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)这个方法用于接受申请权限后用户对权限操作的结果，可以看出来结果和权限时以一对的形式出现的。比如说permission[0] 对应grantResults[0],以此类推。
grantRsults的值也是3种类型和2中返回的类型是相同的，我们只需要对不同的类型进行判断就行了。

## 4.说再多都不如看代码

```java    
  /**
     * Activity
     * 判断权限
     * 主要判断权限6.0以上的机型
     * 需要在调用此方法的类中实现 onRequestPermissionsResult
     */
    @TargetApi(Build.VERSION_CODES.M)
    public static void openMyActivityPermission(Activity context, String permission,int requestCode){
        int result = PermissionChecker.checkSelfPermission(context,permission);
        if (result == PermissionChecker.PERMISSION_GRANTED){
            //有权限
        }else {
            //没有权限
            //判断能否显示系统弹框
            boolean isShow = context.shouldShowRequestPermissionRationale(permission);
            if (!isShow){
                //不能显示系统弹框，这里只能手动提示用户
            }else {
                //帮用户弹出系统弹框
                context.requestPermissions(new String[]{permission},requestCode);
            }
        }
    }
    
    //用户选择后回调
    @Override
    public void onRequestPermissionsResult(int requestCode,@NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode,permissions, grantResults);
    }   
```

### 5.再说运行时权限管理
这个运行时权限管理是goole6.0的加入的一个东西。但是在碎片化极其严重的android中，不可能只有6.0的手机。所以仅仅靠6.0是不现实的。所以我们还要更多的考虑6.0以下的权限适配问题。由于各个厂家的深度定制（鸡肋～～～），很多厂家都自己实现了运行时权限提醒等一系列温馨的功能（各种奇葩～～），所以这时就需要我们开发者自己想办法解决这些问题。   

比如说6.0以下我们需要相机这个权限，但是用户在安装app的时候没有勾选相机权限，或者当弹出权限询问框的时候用户点击了禁止我们该怎么办。目前我知道的就只有try catch来解决这个问题。

