---
title: "Leetcode-1768"
date: 2021-02-24T16:51:02-05:00
toc: false
images:
tags: [code,strings, golang]
  

---
[1768. Merge Strings Alternately
](https://leetcode.com/problems/merge-strings-alternately/)
```
func mergeAlternately(word1 string, word2 string) string {
mergestringres := ""
for i, i2 := range word1 {
mergestringres += string(i2)

		if len(word2) > i {
			mergestringres += string(word2[i])
			
		}
	}
	if len(word1) < len(word2) {
		mergestringres += word2[len(word1):]
	}
	
	return mergestringres
}
```

