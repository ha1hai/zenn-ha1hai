---
title: 'Youtube Data API を利用して Youtube の動画カテゴリーを取得する'
emoji: '🐥'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['youtube', 'python']
published: false
---

```python
import os
from dotenv import load_dotenv
from googleapiclient.discovery import build

# 環境変数をロード
load_dotenv()

# YouTube API キーを取得
API_KEY = os.getenv("YOUTUBE_API_KEY")

# YouTube API クライアントを作成
youtube = build("youtube", "v3", developerKey=API_KEY)


def get_youtube_video_categories(region_code="JP"):
    """ 日本のビデオカテゴリー一覧を取得する関数"""
    try:
        # API 呼び出し
        request = youtube.videoCategories().list(
            part="snippet",
            regionCode=region_code
        )
        response = request.execute()

        # カテゴリー一覧を表示
        categories = response.get("items", [])
        for category in categories:
            # print(category)
            print(f"ID: {category['id']}, Title: {category['snippet']['title']} Assignable: {
                  category['snippet']['assignable']}")

    except Exception as e:
        print(f"An error occurred: {e}")


# 実行
if __name__ == "__main__":
    get_youtube_video_categories()
```
