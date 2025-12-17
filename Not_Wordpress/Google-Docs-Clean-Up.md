## **How to Use the “Clean Up Tools” Google Docs Script**

This Google Apps Script adds a **custom “🧹 Clean Up Tools” menu** to your Google Docs interface.  
It removes unwanted formatting elements — like emojis, em/en dashes, and text-based horizontal rules — while **keeping your original text styling intact**.

### **Setup Instructions**

1. **Open your Google Doc**  
   The document you want to clean must be open.  
2. **Open the Script Editor**  
   * Click **Extensions → Apps Script**  
   * Delete any existing code and **paste this entire script** into the editor.  
3. **Save and Authorize**  
   * Click the **💾 Save** icon and give your project a name.  
   * Press **▶ Run → onOpen** once to trigger permissions.  
   * Approve the authorization prompt (you’ll only do this once).  
4. **Reload Your Google Doc**  
   After saving, refresh the document. You’ll see a new menu at the top:  
   **🧹 Clean Up Tools**  
5. **Run the Cleaner**  
   * Go to **🧹 Clean Up Tools → Run Cleanup**  
   * The script will:  
     * Remove emojis 🧽  
     * Replace em/en dashes (— / –) with commas  
     * Delete text-based horizontal lines (`---`, `___`, `***`)  
     * Remove invisible characters and empty paragraphs  
     * Keep all existing formatting intact (bold, italics, links, etc.)  
6. **Done\!**  
   When it finishes, you’ll get a popup message confirming how many items were cleaned.

---

✅ **Tip:** You can modify the `.replace()` line if you prefer em dashes to be replaced with “and” instead of a comma.  
Just change:

```javascript
.replace(/\s*[–—]\s*/g, ', ')
```

to

```javascript
.replace(/\s*[–—]\s*/g, ' and ')
```


---

## Clean Up Tools Google Docs Script

```
function onOpen() {
  const ui = DocumentApp.getUi();
  ui.createMenu('🧹 Clean Up Tools')
    .addItem('Run Cleanup', 'cleanGoogleDocPreserveFormatting')
    .addToUi();
}

function cleanGoogleDocPreserveFormatting() {
  const body = DocumentApp.getActiveDocument().getBody();
  let changes = 0;

  const emojiRegex = /([\u{1F300}-\u{1F6FF}]|[\u{1F900}-\u{1F9FF}]|[\u{2600}-\u{27BF}]|[\u{1F1E6}-\u{1F1FF}]|[\u{1FA70}-\u{1FAFF}]|[\u{1F700}-\u{1F77F}]|[\u{1F780}-\u{1F7FF}]|[\u{1F800}-\u{1F8FF}])/gu;
  const hrTextRegex = /^-{3,}$|^_{3,}$|^\*{3,}$/; // text-based HRs

  // Recursive function to remove all HORIZONTAL_RULE elements
  function removeHorizontalRules(element) {
    const numChildren = element.getNumChildren?.() || 0;
    for (let i = numChildren - 1; i >= 0; i--) {
      const child = element.getChild(i);
      if (!child) continue;

      if (child.getType() === DocumentApp.ElementType.HORIZONTAL_RULE) {
        element.removeChild(child);
        changes++;
      } else {
        removeHorizontalRules(child);
      }
    }
  }

  removeHorizontalRules(body);

  // Clean text but preserve formatting
  const paragraphs = body.getParagraphs();
  paragraphs.forEach(p => {
    const textElement = p.editAsText?.();
    if (!textElement) return;

    const text = p.getText().trim();

    // Remove HR-like text lines (---, ___, ***, etc)
    if (hrTextRegex.test(text)) {
      p.removeFromParent();
      changes++;
      return;
    }

    // Remove empty paragraphs or invisible chars
    const cleanedText = text.replace(/[\u200B-\u200D\uFEFF]/g, '').trim();
    if (!cleanedText) {
      p.removeFromParent();
      changes++;
      return;
    }

    // Replace en/em dashes and remove emojis
    const cleaned = text
      .replace(/\s*[–—]\s*/g, ', ')
      .replace(emojiRegex, '');

    if (cleaned !== text) {
      textElement.setText(cleaned);
      changes++;
    }
  });

  DocumentApp.getUi().alert(`✅ Clean-up complete! ${changes} items updated or removed.`);
}
xxx
```
