# PDF Split / Combine / Compress


**Summary Table**

| Step         | Tool         | Command Example                                                                 |
|--------------|--------------|--------------------------------------------------------------------------------|
| Split pages  | pdfseparate  | `pdfseparate -f 199 -l 386 input.pdf page_%d.pdf`                              |
| Combine      | pdfunite     | `pdfunite page_*.pdf output.pdf`                                               |
| Compress     | ghostscript  | `ghostscript -sDEVICE=pdfwrite -dPDFSETTINGS=/ebook -sOutputFile=small.pdf output.pdf`  |





**1. Splitting the PDF**

You used `pdfseparate` to extract pages 199–386:

```
pdfseparate -f 199 -l 386 CSAPP_2016.pdf page_%d.pdf
```
This created one PDF file per page.

---

**2. Combining the Pages**

You used `pdfunite` to merge the individual page PDFs:

```
pdfunite page_*.pdf chapter_3.pdf
```
This produced `chapter_3.pdf` containing the selected page range.

---

**3. Observed Issue**

- The merged PDF (`chapter_3.pdf`) was much larger than the original.
- This is common with `pdfseparate` + `pdfunite` because resources (fonts, images) may be duplicated or not optimized.

---

**4. Solution: Compress/Optimize the Output**

You can compress the merged PDF using Ghostscript:

```
ghostscript -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 -dPDFSETTINGS=/ebook \
   -dNOPAUSE -dQUIET -dBATCH -sOutputFile=chapter_3_small.pdf chapter_3.pdf
```
This will create a smaller, optimized PDF.

