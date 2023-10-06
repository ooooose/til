# RequestとResponseについて
## 概要
クライアント（ブラウザなど）からAPIにデータを送信する必要があるとき、データを**リクエストボディ（request body）**として送る。<br />


**リクエスト**ボディはクライアントによってAPIへ送られる。**レスポンスボディ**はAPIがクライアントに送るデータのことを指す。<br />


APIはほとんどの場合、**レスポンス**ボディを送らなければならない。しかし、クライアントは必ずしも**リクエスト**ボディを送らなければいけないわけではない。<br />


**リクエスト**ボディを宣言するために`Pydantic`モデルを使用する。<br />

## `Request`の作り方


## `Response`について
FastAPIの`Response`クラスあHTTPレスポンスを作成するためのユーティリティ。<br />
通常、FastAPIアプリケーション内でHTTPレスポンスをカスタマイズする必要がある場合に使用される。<br />


以下実装例。<br />

```python

from fastapi import FastAPI, Response

app = FastAPI()

@app.get("/custom_response/")
def custom_response():
    content = "This is a custom response."
    
    # Responseオブジェクトを作成し、HTTPステータスコードとレスポンスボディを設定 response = Response(content=content, status_code=200)
    
    # レスポンスヘッダーを設定
    response.headers["X-Custom-Header"] = "Header Value"
    
    return response

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="127.0.0.1", port=8000)

```

`custom_response`エンドポイントはHTTPステータスコード200とレスポンスボディを持つレスポンスを返す。<br />
`Response`クラスを使用することで、HTTPステータスコード、ヘッダー、レスポンスボディなど、レスポンスのさまざまな側面をカスタマイズできる。<br />



## 参考文献
- [公式ドキュメント](https://fastapi.tiangolo.com/ja/tutorial/body/)<br />
