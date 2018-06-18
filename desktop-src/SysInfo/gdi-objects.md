---
Description: GDI objects support only one handle per object. Handles to GDI objects are private to a process. That is, only the process that created the GDI object can use the object handle.
ms.assetid: 699de25c-083d-4be3-a997-67418b7173e1
title: GDI Objects
ms.technology: desktop
ms.prod: windows
ms.author: windowssdkdev
ms.topic: article
ms.date: 05/31/2018
---

# GDI Objects

GDI objects support only one handle per object. Handles to GDI objects are private to a process. That is, only the process that created the GDI object can use the object handle.

There is a theoretical limit of 65,536 GDI handles per session. However, the maximum number of GDI handles that can be opened per session is usually lower, since it is affected by available memory.

**Windows 2000:** There is a limit of 16,384 GDI handles per session.

There is also a default per-process limit of GDI handles. To change this limit, set the following registry value:

**HKEY\_LOCAL\_MACHINE**\\**SOFTWARE**\\**Microsoft**\\**Windows NT**\\**CurrentVersion**\\**Windows**\\**GDIProcessHandleQuota**

This value can be set to a number between 256 and 65,536.

**Windows 2000:** This value can be set to a number between 256 and 16,384.

## Managing GDI Objects

The following table lists the GDI objects, along with each object's creator and destroyer functions. The creator functions either create the object and an object handle or simply return the existing object handle. The destroyer functions remove the object from memory, which invalidates the object handle.



| GDI object           | Creator function                                                                                                                                                                                                                                                                                                                                                                                                                                                                         | Destroyer function                                           |
|----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------|
| Bitmap               | [**CreateBitmap**](https://msdn.microsoft.com/library/windows/desktop/dd183485), [**CreateBitmapIndirect**](https://msdn.microsoft.com/library/windows/desktop/dd183486), [**CreateCompatibleBitmap**](https://msdn.microsoft.com/library/windows/desktop/dd183488), [**CreateDIBitmap**](https://msdn.microsoft.com/library/windows/desktop/dd183491), [**CreateDIBSection**](https://msdn.microsoft.com/library/windows/desktop/dd183494), [**CreateDiscardableBitmap**](https://msdn.microsoft.com/library/windows/desktop/dd183495)                                                                                                                                                                                 | [**DeleteObject**](https://msdn.microsoft.com/library/windows/desktop/dd183539)                         |
| Brush                | [**CreateBrushIndirect**](https://msdn.microsoft.com/library/windows/desktop/dd183487), [**CreateDIBPatternBrush**](https://msdn.microsoft.com/library/windows/desktop/dd183492), [**CreateDIBPatternBrushPt**](https://msdn.microsoft.com/library/windows/desktop/dd183493), [**CreateHatchBrush**](https://msdn.microsoft.com/library/windows/desktop/dd183504), [**CreatePatternBrush**](https://msdn.microsoft.com/library/windows/desktop/dd183508), [**CreateSolidBrush**](https://msdn.microsoft.com/library/windows/desktop/dd183518)                                                                                                                                                                     | [**DeleteObject**](https://msdn.microsoft.com/library/windows/desktop/dd183539)                         |
| DC                   | [**CreateDC**](https://msdn.microsoft.com/library/windows/desktop/dd183490)                                                                                                                                                                                                                                                                                                                                                                                                                                                             | [**DeleteDC**](https://msdn.microsoft.com/library/windows/desktop/dd183533), [**ReleaseDC**](https://msdn.microsoft.com/library/windows/desktop/dd162920) |
| Enhanced metafile    | [**CreateEnhMetaFile**](https://msdn.microsoft.com/library/windows/desktop/dd183498)                                                                                                                                                                                                                                                                                                                                                                                                                                           | [**DeleteEnhMetaFile**](https://msdn.microsoft.com/library/windows/desktop/dd183534)               |
| Enhanced-metafile DC | [**CreateEnhMetaFile**](https://msdn.microsoft.com/library/windows/desktop/dd183498)                                                                                                                                                                                                                                                                                                                                                                                                                                           | [**CloseEnhMetaFile**](https://msdn.microsoft.com/library/windows/desktop/dd183442)                 |
| Font                 | [**CreateFont**](https://msdn.microsoft.com/library/windows/desktop/dd183499), [**CreateFontIndirect**](https://msdn.microsoft.com/library/windows/desktop/dd183500)                                                                                                                                                                                                                                                                                                                                                                                                       | [**DeleteObject**](https://msdn.microsoft.com/library/windows/desktop/dd183539)                         |
| Memory DC            | [**CreateCompatibleDC**](https://msdn.microsoft.com/library/windows/desktop/dd183489)                                                                                                                                                                                                                                                                                                                                                                                                                                         | [**DeleteDC**](https://msdn.microsoft.com/library/windows/desktop/dd183533)                                 |
| Metafile             | [**CreateMetaFile**](https://msdn.microsoft.com/library/windows/desktop/dd183506)                                                                                                                                                                                                                                                                                                                                                                                                                                                 | [**DeleteMetaFile**](https://msdn.microsoft.com/library/windows/desktop/dd183537)                     |
| Metafile DC          | [**CreateMetaFile**](https://msdn.microsoft.com/library/windows/desktop/dd183506)                                                                                                                                                                                                                                                                                                                                                                                                                                                 | [**CloseMetaFile**](https://msdn.microsoft.com/library/windows/desktop/dd183445)                       |
| Palette              | [**CreatePalette**](https://msdn.microsoft.com/library/windows/desktop/dd183507)                                                                                                                                                                                                                                                                                                                                                                                                                                                   | [**DeleteObject**](https://msdn.microsoft.com/library/windows/desktop/dd183539)                         |
| Pen and extended pen | [**CreatePen**](https://msdn.microsoft.com/library/windows/desktop/dd183509), [**CreatePenIndirect**](https://msdn.microsoft.com/library/windows/desktop/dd183510), [**ExtCreatePen**](https://msdn.microsoft.com/library/windows/desktop/dd162705)                                                                                                                                                                                                                                                                                                                                                                     | [**DeleteObject**](https://msdn.microsoft.com/library/windows/desktop/dd183539)                         |
| Region               | [**CombineRgn**](https://msdn.microsoft.com/library/windows/desktop/dd183465), [**CreateEllipticRgn**](https://msdn.microsoft.com/library/windows/desktop/dd183496), [**CreateEllipticRgnIndirect**](https://msdn.microsoft.com/library/windows/desktop/dd183497), [**CreatePolygonRgn**](https://msdn.microsoft.com/library/windows/desktop/dd183511), [**CreatePolyPolygonRgn**](https://msdn.microsoft.com/library/windows/desktop/dd183512), [**CreateRectRgn**](https://msdn.microsoft.com/library/windows/desktop/dd183514), [**CreateRectRgnIndirect**](https://msdn.microsoft.com/library/windows/desktop/dd183515), [**CreateRoundRectRgn**](https://msdn.microsoft.com/library/windows/desktop/dd183516), [**ExtCreateRegion**](https://msdn.microsoft.com/library/windows/desktop/dd162706), [**PathToRegion**](https://msdn.microsoft.com/library/windows/desktop/dd162780) | [**DeleteObject**](https://msdn.microsoft.com/library/windows/desktop/dd183539)                         |



 

 

 


