# Breakdance Form → Auto-Download Lead Magnet on Submit

Implementation for triggering file downloads when users submit a Breakdance form. No extra plugins needed.

---

## Form Setup in Breakdance

### 1. Form Settings

**Form Name:** `LM-MyDownload` (or your preferred name)

**Actions:** Enable these as needed
- ✅ **Popup (Optional)** 
  - **Popup on Success** - Backup download link in case auto-download fails
  - **Popup on Error** - User-friendly error message
- ✅ **Email** - Send the file via email as a backup delivery method
- ✅ **Other Actions** - Add any integrations (CRM, Mailchimp, etc.)

**Built-in JS:** Leave empty - we'll use a separate code block instead (works more reliably)

---

### 2. Form Advanced Settings

**Form HTML ID:** `MyDownload`  
**Submit Button HTML ID:** `MyDownload-submit`

> 💡 **Tip:** Use descriptive IDs that match your lead magnet name. Makes it easier to manage multiple forms.

---

## Add the Download Script

### 3. Insert Code Block

Add a **Code Block** element near your form (above or below, doesn't matter).

Paste this code and customize the highlighted sections:

```html
<script>
document.addEventListener("DOMContentLoaded", function () {
  
  // Match this to your Form HTML ID
  const form = document.getElementById("MyDownload");
  
  if (!form) {
    console.error("Form not found - check Form HTML ID");
    return;
  }
  
  form.addEventListener("submit", function (event) {
    event.preventDefault();
    
    // Let Breakdance process the form submission first
    setTimeout(() => {
      
      // Hide the form
      form.style.display = "none";
      
      // Show success message
      let successMsg = document.createElement("div");
      successMsg.id = "formSuccess";
      successMsg.innerHTML = "✅ <strong>Thanks!</strong> Your download is starting...";
      successMsg.style.cssText = "padding: 20px; background: #4CAF50; color: white; border-radius: 8px; text-align: center; font-size: 18px; margin-top: 20px;";
      form.parentNode.appendChild(successMsg);
      
      // Trigger the download
      const link = document.createElement("a");
      link.href = "/wp-content/uploads/2025/08/original-file-name.pdf"; // ← Your file path
      link.download = "BusinessName-File.pdf"; // ← User's download filename
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
      
    }, 800); // 800ms delay lets Breakdance process the form first
  });
});
</script>
```

---

## Customization Guide

### 📝 What to Change

| Line | What It Does | How to Customize |
|------|-------------|------------------|
| `getElementById("MyDownload")` | Targets your form | Change `"MyDownload"` to match your **Form HTML ID** |
| `link.href = "..."` | Path to your file | Upload file to Media Library, copy URL, paste here |
| `link.download = "..."` | Filename users see | Choose a clear, descriptive name like `"Your-Business-Guide.pdf"` |
| `setTimeout(..., 800)` | Delay before download | Increase if form processes slowly (try 1200ms) |

---

## File Upload Instructions

### Where to Put Your Lead Magnet

**Option 1: Media Library (Recommended)**
1. Go to **Media → Add New**
2. Upload your PDF/file
3. Click the file → Copy URL
4. Paste into `link.href`

**Option 2: Direct Server Upload**
1. Upload to `/wp-content/uploads/lead-magnets/`
2. Use path: `/wp-content/uploads/lead-magnets/your-file.pdf`

> ⚠️ **Security Note:** Files in `/wp-content/uploads/` are publicly accessible. For protected content, use a plugin like Download Monitor or restrict via .htaccess.

---

## Styling the Success Message

The script includes basic inline styles. To customize:

```javascript
successMsg.style.cssText = "
  padding: 20px; 
  background: #4CAF50;        /* ← Change background color */
  color: white;               /* ← Change text color */
  border-radius: 8px; 
  text-align: center; 
  font-size: 18px;            /* ← Adjust text size */
  margin-top: 20px;
";
```

Or remove inline styles and add a CSS class:
```javascript
successMsg.classList.add("my-custom-success");
```

Then style in your global CSS.

---

## Troubleshooting

### Download doesn't trigger?
- Check browser console for errors (F12 → Console tab)
- Verify Form HTML ID matches exactly (case-sensitive)
- Confirm file path is correct (try opening the URL directly in browser)
- Increase `setTimeout` delay to 1200ms

### Form submits but doesn't hide?
- Check that `form.style.display = "none"` is executing
- Verify no other JavaScript is conflicting
- Try adding `!important`: `form.style.cssText = "display: none !important;"`

### File downloads but form still shows?
- Breakdance might be refreshing the page
- Add this at the top of the submit handler:
```javascript
form.addEventListener("submit", function (event) {
  event.preventDefault();
  event.stopPropagation(); // ← Add this line
```

### Multiple forms on one page?
- Use unique Form HTML IDs for each form
- Duplicate the script and change the `getElementById()` values
- Or create separate code blocks for each form

---

## Multiple Downloads on One Page

If you have multiple forms with downloads:

```javascript
// Form 1
const form1 = document.getElementById("MyDownload");
if (form1) {
  form1.addEventListener("submit", function(e) {
    e.preventDefault();
    // ... download logic with unique file path
  });
}

// Form 2
const form2 = document.getElementById("MyOtherDownload");
if (form2) {
  form2.addEventListener("submit", function(e) {
    e.preventDefault();
    // ... download logic with different file
  });
}
```

---

## Best Practices

✅ **Always test in incognito mode** - Caching can hide issues  
✅ **Test on mobile** - Some browsers block auto-downloads  
✅ **Provide a backup link** - Use Popup on Success with manual download button  
✅ **Track conversions** - Add GTM event tracking to the download trigger  
✅ **Use descriptive filenames** - `"SEO-Checklist-2025.pdf"` is better than `"download.pdf"`  

---

## Optional: Add Google Analytics Tracking

Insert this before `link.click()`:

```javascript
// Track download in GA4
if (typeof gtag !== 'undefined') {
  gtag('event', 'file_download', {
    'file_name': 'My-Lead-Magnet-Download.pdf',
    'file_extension': 'pdf',
    'link_text': 'Quote Bundle Form'
  });
}
```

---

**Need help?** Message me https://brighterwebsites.com.au/support/  and drop the console error message we can troubleshoot! 🚀
