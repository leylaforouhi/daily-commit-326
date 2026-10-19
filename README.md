import json
from pathlib import Path

BOOKMARKS_FILE = Path("bookmarks.json")


def load_bookmarks():
    if not BOOKMARKS_FILE.exists():
        return []

    try:
        return json.loads(BOOKMARKS_FILE.read_text(encoding="utf-8"))
    except (json.JSONDecodeError, OSError):
        return []


def save_bookmarks(bookmarks):
    BOOKMARKS_FILE.write_text(
        json.dumps(bookmarks, indent=2, ensure_ascii=False),
        encoding="utf-8",
    )


def add_bookmark(bookmarks, title, url, category="General"):
    if not title.strip() or not url.strip():
        raise ValueError("Title and URL cannot be empty")

    bookmark = {
        "title": title.strip(),
        "url": url.strip(),
        "category": category.strip() or "General",
    }

    if any(item["url"] == bookmark["url"] for item in bookmarks):
        raise ValueError("This URL is already bookmarked")

    bookmarks.append(bookmark)
    save_bookmarks(bookmarks)


def search_bookmarks(bookmarks, keyword):
    keyword = keyword.lower()

    return [
        item for item in bookmarks
        if keyword in item["title"].lower()
        or keyword in item["category"].lower()
        or keyword in item["url"].lower()
    ]


if __name__ == "__main__":
    bookmarks = load_bookmarks()

    try:
        add_bookmark(
            bookmarks,
            "Python Documentation",
            "https://docs.python.org/3/",
            "Programming",
        )

        print("Bookmarks:")
        for item in bookmarks:
            print(f"[{item['category']}] {item['title']} — {item['url']}")

    except ValueError as error:
        print("Notice:", error)
