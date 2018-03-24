# Android 웹뷰로 개발할 시에

## Webview 기본 코드

- 웹뷰 초기화 코드

```java
public WebView mWebView;
public void setWebview(){
  // ...
  mWebView = (WebView) findViewById(R.id.webview);
  mWebView.getSettings().setPluginState(WebSettings.PluginState.ON);
  mWebView.getSettings().setJavaScriptEnabled(true);
  mWebView.getSettings().setUseWideViewPort(true);
  mWebView.getSettings().setRenderPriority(WebSettings.RenderPriority.HIGH);
  mWebView.getSettings().setLayoutAlgorithm(WebSettings.LayoutAlgorithm.SINGLE_COLUMN);
  mWebView.setHorizontalScrollBarEnabled(false);
  mWebView.getSettings().setCacheMode(WebSettings.LOAD_NO_CACHE);
  mWebView.setScrollBarStyle(WebView.SCROLLBARS_OUTSIDE_OVERLAY);
  mWebView.getSettings().setSupportMultipleWindows(true);
  mWebView.setWebViewClient(new WebBrowserClient());
  mWebView.setWebChromeClient(new NewWebChromeClient());  
}
```

## 페이지 이동시 처리

- 주의 loadurl() 메소드 등으로 호출된 내용은 수행되지 않음

```java
public class WebBrowserClient extends WebViewClient {
    @Override
    public void onPageStarted(WebView view, String url, Bitmap favicon) {
        // TODO 인터넷 연결 상태 체크
        super.onPageStarted(view, url, favicon);
    }
    @Override
    public void onPageFinished(WebView view, String url) {
        Log.e(TAG, "finish url: " + url);
        super.onPageFinished(view, url);
    }

    @Override
    public boolean shouldOverrideUrlLoading(WebView view, String url) {
        Log.e("T_T", "url:"+url);
        if(url.equals(StaticObject.SERVER_URL+"/")) {
            // finish();
        }
        else if(url.equals(StaticObject.SERVER_URL+"/app/sign/out")){
            // ...
            return true;
        }else if(url.startsWith(StaticObject.SERVER_URL+"/app/")){
            return true;
        }
        return super.shouldOverrideUrlLoading(view, url);
    }
}
```

## Post url로 request 보내는 방법

- url encode

## request header

-

```java
Map<String, String> extraHeaders = new HashMap<String, String>();
extraHeaders.put("token", StaticObject.authToken); // 헤더 값 추가
mWebView.loadUrl( StaticObject.SERVER_URL + "/web/home", extraHeaders);
extraHeaders.clear(); // 헤더 비우기
```

## 웹뷰에 이미지 업로드 처리 코드

- 주의 : 4.4.2 버전은 뭘해도 안됨... ㅠㅠ

```java
public final int FILECHOOSER_NORMAL_REQ_CODE = 0x2202;
public final int FILECHOOSER_LOLLIPOP_REQ_CODE = 0x3303;
public ValueCallback<Uri> filePathCallbackNormal;
public ValueCallback<Uri[]> filePathCallbackLollipop;
// Inner class로 사용해야함
public class New2WebChromeClient extends WebChromeClient {
    // For Android < 3.0
    public  void openFileChooser( ValueCallback<Uri> uploadMsg) {
        Log.d("MainActivity","3.0 <");
        openFileChooser(uploadMsg,"");
    }
    // For Android 3.0+
    public void openFileChooser( ValueCallback<Uri> uploadMsg, String acceptType) {
        Log.d("MainActivity", "3.0+");
        filePathCallbackNormal = uploadMsg;
        Intent i = new Intent(Intent.ACTION_GET_CONTENT);
        i.addCategory(Intent.CATEGORY_OPENABLE);
        i.setType("image/*");

        startActivityForResult(Intent.createChooser(i, "File Chooser"), FILECHOOSER_NORMAL_REQ_CODE);
    }

    // For Android 4.1+
    public void openFileChooser(ValueCallback<Uri> uploadMsg, String acceptType, String capture) {
        Log.d("MainActivity", "4.1+");
        openFileChooser(uploadMsg, acceptType);
    }

    // For Android 5.0+
    public boolean onShowFileChooser(WebView webView, ValueCallback<Uri[]> filePathCallback,
            WebChromeClient.FileChooserParams fileChooserParams) {
        Log.d("MainActivity", "5.0+");

        if (filePathCallbackLollipop != null) {
            filePathCallbackLollipop.onReceiveValue(null);
            filePathCallbackLollipop = null;
        }
        filePathCallbackLollipop = filePathCallback;
        Intent i = new Intent(Intent.ACTION_GET_CONTENT);
        i.addCategory(Intent.CATEGORY_OPENABLE);
        i.setType("image/*");

        startActivityForResult(Intent.createChooser(i, "File Chooser"), FILECHOOSER_LOLLIPOP_REQ_CODE);

        return true;
    }
}


@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (requestCode == FILECHOOSER_NORMAL_REQ_CODE) {
        if (filePathCallbackNormal == null ) return;
        Uri result = (data == null || resultCode != RESULT_OK) ? null : data.getData();
        filePathCallbackNormal.onReceiveValue(result);
        filePathCallbackNormal = null ;
    } else if (requestCode == FILECHOOSER_LOLLIPOP_REQ_CODE) {
        if (filePathCallbackLollipop == null ) return ;
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
            filePathCallbackLollipop.onReceiveValue(WebChromeClient.FileChooserParams.parseResult(resultCode, data));
        }
        filePathCallbackLollipop = null ;
    }
}

```
