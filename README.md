# Google-Drive-View-Only-PDF-Script-Downloader

Here you can use this script to download view only pdf file from Google Drive. This script works like a screenshot capturing all pdf pages to bulk of images with better resolution quality and combine it all into one pdf file.

### Big-Note: Use this script wisely!

### Instruction
1. Open the PDF from Google Drive,
2. Click Preview,
3. Then on top right page click on three vertical dots menu -> Open in new window,
4. Scroll down pdf to end of content and make sure all of content is loaded,
5. Inspect element your browser,
6. Copy and paste script below on console tab,
   ```js
   (function () {
     console.log("Loading script ...");
   
     let script = document.createElement("script");
     script.onload = function () {
       const { jsPDF } = window.jspdf;
   
       // Generate a PDF from images with "blob:" sources.
       let pdf = null;
       let imgElements = document.getElementsByTagName("img");
       let validImgs = [];
   
       console.log("Scanning content ...");
       for (let i = 0; i < imgElements.length; i++) {
         let img = imgElements[i];
   
         // specific check for Google Drive blob images
         let checkURLString = "blob:https://drive.google.com/";
         if (img.src.substring(0, checkURLString.length) !== checkURLString) {
           continue;
         }
   
         validImgs.push(img);
       }
   
       console.log(`${validImgs.length} content found!`);
       console.log("Generating PDF file ...");
   
       for (let i = 0; i < validImgs.length; i++) {
         let img = validImgs[i];
         
         // Convert image to DataURL via Canvas
         let canvasElement = document.createElement("canvas");
         let con = canvasElement.getContext("2d");
         canvasElement.width = img.naturalWidth;
         canvasElement.height = img.naturalHeight;
         con.drawImage(img, 0, 0, img.naturalWidth, img.naturalHeight);
         let imgData = canvasElement.toDataURL();
   
         // Determine orientation and dimensions for THIS specific image
         let orientation = img.naturalWidth > img.naturalHeight ? "l" : "p";
         let pageWidth = img.naturalWidth;
         let pageHeight = img.naturalHeight;
   
         if (i === 0) {
           // Initialize PDF with the dimensions of the FIRST image
           pdf = new jsPDF({
             orientation: orientation,
             unit: "px",
             format: [pageWidth, pageHeight],
           });
         } else {
           // For subsequent images, add a new page with THAT image's specific dimensions
           pdf.addPage([pageWidth, pageHeight], orientation);
         }
   
         // Add the image to the current page
         pdf.addImage(imgData, "PNG", 0, 0, pageWidth, pageHeight, "", "SLOW");
   
         const percentages = Math.floor(((i + 1) / validImgs.length) * 100);
         console.log(`Processing content ${percentages}%`);
       }
   
       // Check if title contains .pdf in end of the title
       // Use optional chaining to avoid errors if the meta tag isn't present.
       // Fall back to document.title when necessary. Note: if the PDF is inside a cross-origin iframe,
       // parent scripts cannot access the iframe document due to same-origin policy.
       let title = document.querySelector('meta[itemprop="name"]')?.content || document.title || 'download.pdf';
       if ((title.split(".").pop() || "").toLowerCase() !== "pdf") {
         title = title + ".pdf";
       }
   
       // Download the generated PDF.
       console.log("Downloading PDF file ...");
       pdf.save(title, { returnPromise: true }).then(() => {
         document.body.removeChild(script);
         console.log("PDF downloaded!");
       });
     };
   
     // Load the jsPDF library using the trusted URL.
     let scriptURL = "https://unpkg.com/jspdf@latest/dist/jspdf.umd.min.js";
     let trustedURL;
     if (window.trustedTypes && trustedTypes.createPolicy) {
       const policy = trustedTypes.createPolicy("myPolicy", {
         createScriptURL: (input) => {
           return input;
         },
       });
       trustedURL = policy.createScriptURL(scriptURL);
     } else {
       trustedURL = scriptURL;
     }
   
     script.src = trustedURL;
     document.body.appendChild(script);
   })();
   ```
6. Wait script processing and downloading pdf file,
7. Fast or slow pdf processing based on the pdf content it self,
8. Enjoyy....

### Source Reference
This script is modified with source from :
- [mhsohan/How-to-download-protected-view-only-files-from-google-drive-](https://github.com/mhsohan/How-to-download-protected-view-only-files-from-google-drive-)
- [zeltox/Google-Drive-PDF-Downloader](https://github.com/zeltox/Google-Drive-PDF-Downloader)
