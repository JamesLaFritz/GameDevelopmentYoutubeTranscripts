Clean up this YouTube video. Remove only filler words like “uh,” “um,” “you know,” “like” (if not semantically meaningful), etc. Do not paraphrase or summarize—keep every sentence, joke, explanation, and technical detail intact. Divide it into logical sections using ### Level 3 Markdown headings.

- Remove filler words only  
- Preserve all content exactly  
- Use ### subheadings

Use the YouTube video’s description as the Summary. If it’s missing, write a concise summary based on the introduction.

Output it in this full format:
```Markdown
# [Title]

**[YouTube Channel (e.g., GDC Talk or Roguelike Celebration) – Video Date]**  

[![Watch on YouTube](https://img.youtube.com/vi/[YOUTUBE-ID]/0.jpg)](https://www.youtube.com/watch?v=[YOUTUBE-ID])
 
## Summary

[YouTube description or summary based on intro]
 
---

---

## Transcript  

### Introduction
  
[Cleaned transcript begins here...]

---

### [Etc.]

_(Subsequent sections)_
```

Do not simplify or skip any part. Include everything the speaker said that isn't a filler word.