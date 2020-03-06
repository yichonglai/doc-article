# 基于[SheetJS js-xlsx](https://github.com/SheetJS/sheetjs)读取本地 `excel` 返回 `json` 数据组件模板

```jsx
import {Button, Upload} from 'antd';
import React, {PureComponent} from 'react';

import XLSX from 'xlsx';

interface ExcelImportProps {
  /**解析状态回调 */
  onParsing?: (isParsing: boolean) => void;
  /**解析成功 */
  onParsed?: (json?: any) => void;
}
interface ExcelImportState {
  isParsing: boolean;
}

class ExcelImport extends PureComponent<ExcelImportProps, ExcelImportState> {
  state = {
    isParsing: false,
  };
  readFile = (e: any) => {
    const {file} = e || {};
    if (!file) {
      // message.error('请选择xlx、xlxs格式文件');
      return;
    } else if (!/\.(xls|xlsx)$/.test(file.name.toLowerCase())) {
      message.error('请选择xlx、xlxs格式文件');
    } else {
      const {onParsing, onParsed} = this.props;
      onParsing && onParsing(true);
      this.setState({isParsing: true});

      const fileReader = new FileReader();
      fileReader.onload = () => {
        try {
          const workbook = XLSX.read(fileReader.result, {
            type: 'buffer',
          });
          const wsname = workbook.SheetNames[0]; // take first sheet
          const json = XLSX.utils.sheet_to_json(workbook.Sheets[wsname]); // generate json
          onParsed && onParsed(json);
        } catch (e) {
          message.error('解析有误，请检查文件内容！');
        }

        onParsing && onParsing(false);
        this.setState({isParsing: false});
      };
      fileReader.onerror = () => {
        onParsing && onParsing(false);
        this.setState({isParsing: false});
      };
      fileReader.readAsArrayBuffer(file);
    }
  };
  render() {
    const {isParsing} = this.state;
    const props = {
      accept: '.xls,.xlsx',
      showUploadList: false,
      disabled: isParsing,
      beforeUpload: () => false,
      onChange: this.readFile,
    };

    return (
      <Upload {...props}>
        <Button type="primary" icon="download" style={{marginRight: '10px'}}>
          开始导入
        </Button>
        (仅支持xls、xlxs格式)
      </Upload>
    );
  }
}

export default ExcelImport;

```
