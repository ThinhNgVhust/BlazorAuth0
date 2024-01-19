```C#
using System;
using System.IO;
using NPOI.HSSF.UserModel;
using NPOI.SS.UserModel;
using NPOI.XSSF.UserModel;

class Program
{
    static void Main()
    {
        ConvertXlsxToXls("input.xlsx", "output.xls");
    }

    static void ConvertXlsxToXls(string xlsxFilePath, string xlsFilePath)
    {
        using (var fs = new FileStream(xlsxFilePath, FileMode.Open, FileAccess.Read))
        {
            var workbookXlsx = new XSSFWorkbook(fs);
            var workbookXls = new HSSFWorkbook();

            // Đối tượng ICellStyle để sao chép style
            ICellStyle newCellStyle;

            for (int i = 0; i < workbookXlsx.NumberOfSheets; i++)
            {
                var sheetXlsx = (XSSFSheet)workbookXlsx.GetSheetAt(i);
                var sheetXls = (HSSFSheet)workbookXls.CreateSheet(sheetXlsx.SheetName);

                for (int rowIndex = 0; rowIndex <= sheetXlsx.LastRowNum; rowIndex++)
                {
                    var rowXlsx = (XSSFRow)sheetXlsx.GetRow(rowIndex);
                    var rowXls = (HSSFRow)sheetXls.CreateRow(rowIndex);

                    if (rowXlsx != null)
                    {
                        for (int cellIndex = 0; cellIndex <= rowXlsx.LastCellNum; cellIndex++)
                        {
                            var cellXlsx = (XSSFCell)rowXlsx.GetCell(cellIndex);
                            var cellXls = (HSSFCell)rowXls.CreateCell(cellIndex);

                            if (cellXlsx != null)
                            {
                                // Sao chép style từ cellXlsx sang cellXls
                                newCellStyle = workbookXls.CreateCellStyle();
                                CopyCellStyle(cellXlsx.CellStyle, newCellStyle);
                                cellXls.CellStyle = newCellStyle;

                                // Sao chép giá trị từ cellXlsx sang cellXls
                                CopyCellValue(cellXlsx, cellXls);
                            }
                        }
                    }
                }
            }

            using (var fileStream = new FileStream(xlsFilePath, FileMode.Create))
            {
                workbookXls.Write(fileStream);
            }

            Console.WriteLine("Chuyển đổi và sao chép style thành công từ .xlsx sang .xls");
        }
    }

    // Hàm sao chép style từ oldStyle sang newStyle
    static void CopyCellStyle(ICellStyle oldStyle, ICellStyle newStyle)
    {
        newStyle.Alignment = oldStyle.Alignment;
        newStyle.BorderBottom = oldStyle.BorderBottom;
        newStyle.BorderLeft = oldStyle.BorderLeft;
        newStyle.BorderRight = oldStyle.BorderRight;
        newStyle.BorderTop = oldStyle.BorderTop;
        newStyle.BottomBorderColor = oldStyle.BottomBorderColor;
        newStyle.DataFormat = oldStyle.DataFormat;
        newStyle.FillForegroundColor = oldStyle.FillForegroundColor;
        newStyle.FillPattern = oldStyle.FillPattern;
        newStyle.Indention = oldStyle.Indention;
        newStyle.LeftBorderColor = oldStyle.LeftBorderColor;
        newStyle.RightBorderColor = oldStyle.RightBorderColor;
        newStyle.Rotation = oldStyle.Rotation;
        newStyle.TopBorderColor = oldStyle.TopBorderColor;
        newStyle.VerticalAlignment = oldStyle.VerticalAlignment;
        newStyle.WrapText = oldStyle.WrapText;
    }

    // Hàm sao chép giá trị từ cellFrom sang cellTo
    static void CopyCellValue(XSSFCell cellFrom, HSSFCell cellTo)
    {
        switch (cellFrom.CellType)
        {
            case CellType.Boolean:
                cellTo.SetCellValue(cellFrom.BooleanCellValue);
                break;
            case CellType.Numeric:
                cellTo.SetCellValue(cellFrom.NumericCellValue);
                break;
            case CellType.String:
                cellTo.SetCellValue(cellFrom.StringCellValue);
                break;
            case CellType.Formula:
                cellTo.CellFormula = cellFrom.CellFormula;
                break;
            default:
                // Xử lý các kiểu dữ liệu khác nếu cần
                break;
        }
    }
}
```
