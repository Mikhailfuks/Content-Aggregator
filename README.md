import feedparser
import datetime

def fetch_rss_feed(url):
    """Fetches and parses an RSS feed."""
    try:
        feed = feedparser.parse(url)
        if feed.bozo: # Check for parsing errors
            print(f"Error parsing feed from {url}: {feed.bozo_exception}")
            return None
        return feed.entries
    except Exception as e:
        print(f"Error fetching feed from {url}: {e}")
        return None


def process_feed_items(feed_items):
    """Processes and formats feed items."""
    if not feed_items:
        return []
    items = []
    for item in feed_items:
        try:
            title = item.title
            link = item.link
            published = item.published # May be None
            summary = item.summary # May be None or long; consider truncation
            items.append({
                "title": title,
                "link": link,
                "published": published,
                "summary": summary
            })
        except AttributeError:
            print("Skipping item due to missing attributes")
    return items


def main():
  feed_urls = [
      "https://www.example.com/rss.xml", # Replace with actual RSS feed URLs
      "https://another-site.com/rss"
  ]

  aggregated_content = []
  for url in feed_urls:
    feed_items = fetch_rss_feed(url)
    if feed_items:
        aggregated_content.extend(process_feed_items(feed_items))

  aggregated_content.sort(key=lambda item: item.get('published', ''), reverse=True)

  for item in aggregated_content:
      print(f"Title: {item['title']}")
      print(f"Link: {item['link']}")
      print(f"Published: {item.get('published','N/A')}")
      print(f"Summary: {item.get('summary','N/A')}\n")

if __name__ == "__main__":
    main()

