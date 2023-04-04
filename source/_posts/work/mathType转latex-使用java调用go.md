---
title: mathType转latex-使用java调用go
date: 2021-06-29 18:00:22
categories: work
tags:
	- mathType
	- go
cover: /images/go.jpeg
typora-root-url: ../..
---

## mathType转latex-java调用go转mathType

### 背景

​		教育行业常使用mathType作为公式编辑器，当解析word资源转为线上资源时，常用java word poi提取word数据，但其中mathType只能被识别为图片，不能正常解析为可编辑公式，此时往往部署另一套C#程序解析word，使用windows部署C#解析word存在解析缓慢、程序易卡死，大多数java开发人员不熟悉C#无法快速迭代的通病，此时亟需使用java解析mathType，从而完全替代C#版本的word解析。

​		mathType是一个第三方的数学公式插件，生成的是一个带有公式矢量图ole对象插入到文档中，.net使用mathType提供的C#包解析ole对象，但使用java程序无法直接解析，.net依赖于windows环境，无法部署到K8S容器中，本文基于java word poi的基础上提出java + go处理mathType转化为latex的解决方案。

### 方案探索

​		目前无较好直接解析mathType的java程序，但反观其它语言却存在解析mathType的解决方案，github中一款由作者zhexiao基于go程序解析mathType的二进制文件为latex程序，此程序直接解析mathType的二进制文件为latex，正好弥补java解析mathType中存在的不足。假想能部署一套go程序，使用基于http请求调用go程序转mathType转latex，将latex公式嵌入word解析中，实现java调用go解析mathType为latex公式似乎是替换java无法直接解析mathType的执行方案。

​		首先部署golang环境，拉取mathType解析latex程序https://github.com/zhexiao/mtef-go运行程序，运行作者示例，然后执行一下命名

```go
go run main.go -f test/oleObject1.bin
```

​		得到编译结果如下

```
$$ \frac { -b±\sqrt[] { b ^ { 2 } -4ac } } { 2a } $$
```

​		此时证明java + go处理mathType转化为latex方案的可行性，接下来就是如何将word中mathType中的二进制文件提取出来，观察以下mathType在word中的xml结构，o:OLEObject代表一个公式，ProgID="Equation.DSMT4"表示为MathType类型，r:id="rId6"可作为该二进制文件的唯一id

```xml
<xml-fragment xmlns:wpc="http://schemas.microsoft.com/office/word/2010/wordprocessingCanvas" xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006" xmlns:o="urn:schemas-microsoft-com:office:office" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:m="http://schemas.openxmlformats.org/officeDocument/2006/math" xmlns:v="urn:schemas-microsoft-com:vml" xmlns:wp14="http://schemas.microsoft.com/office/word/2010/wordprocessingDrawing" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:w14="http://schemas.microsoft.com/office/word/2010/wordml" xmlns:w10="urn:schemas-microsoft-com:office:word" xmlns:w15="http://schemas.microsoft.com/office/word/2012/wordml" xmlns:wpg="http://schemas.microsoft.com/office/word/2010/wordprocessingGroup" xmlns:wpi="http://schemas.microsoft.com/office/word/2010/wordprocessingInk" xmlns:wne="http://schemas.microsoft.com/office/word/2006/wordml" xmlns:wps="http://schemas.microsoft.com/office/word/2010/wordprocessingShape" xmlns:wpsCustomData="http://www.wps.cn/officeDocument/2013/wpsCustomData">
  <w:rPr>
    <w:rFonts w:ascii="Times New Roman" w:hAnsi="Times New Roman"/>
    <w:position w:val="-10"/>
  </w:rPr>
  <w:object>
    <v:shape id="_x0000_i1025" o:spt="75" type="#_x0000_t75" style="height:15pt;width:6.75pt;" o:ole="t" filled="f" o:preferrelative="t" stroked="f" coordsize="21600,21600">
      <v:path/>
      <v:fill on="f" focussize="0,0"/>
      <v:stroke on="f" joinstyle="miter"/>
      <v:imagedata r:id="rId7" o:title=""/>
      <o:lock v:ext="edit" aspectratio="t"/>
      <w10:wrap type="none"/>
      <w10:anchorlock/>
    </v:shape>
    <o:OLEObject Type="Embed" ProgID="Equation.DSMT4" ShapeID="_x0000_i1025" DrawAspect="Content" ObjectID="_1468075725" r:id="rId6">
      <o:LockedField>false</o:LockedField>
    </o:OLEObject>
  </w:object>
</xml-fragment>
```

​		java word poi获取mathType二进制文件，用r:id属性映射每个二进制文件为Map结构，方便替换mathType节点，由于word.getPartById(binRid)获取到的是inputStream，go程序接收的参数是二进制文件，需要将二进制文件写入文件供go程序调用。

```java
private Map<String, String> getMath(XWPFRun run, Node runNode) throws IOException {
        Map<String, String> map = Maps.newHashMap();
  			// 获取mathType OLEObject节点
        Node binNode = getChildNode(runNode, "o:OLEObject");
        if (binNode == null) {
            return map;
        }
        XWPFDocument word = run.getDocument();
        NamedNodeMap binAttrs = binNode.getAttributes();
        // 公式二进制文件在Word中的ID
        String binRid = binAttrs.getNamedItem("r:id").getNodeValue();
        // 获取二进制文件
        PackagePart binPart = word.getPartById(binRid);
        String path = "oleObject-" + binRid + ".bin";
        try (FileOutputStream fileOutputStream = new FileOutputStream(path)){
            if (!map.containsKey(binRid)) {
                ByteStreams.copy(binPart.getInputStream(), fileOutputStream);
              	// 调用go程序执行MathType转latex
                String latex = MathTypeToLatex.mathTypeTransformer.Convert(new GoString.ByValue(path));
                if (StringUtils.isNotBlank(latex)) {
                    log.info("getMath->latex转换成功,latex:{}", latex);
                    map.put(binRid, latex);
                }
            }
        } catch (Exception e) {
            log.warn("getMath->mathType转latex出现错误:{}", e);
        }finally {
            Files.delete(Paths.get(path));
        }
        return map;
    }


private Node getChildNode(Node node, String nodeName) {
        if (!node.hasChildNodes()) {
            return null;
        }
        NodeList childNodes = node.getChildNodes();
        for (int i = 0; i < childNodes.getLength(); i++) {
            Node childNode = childNodes.item(i);
            if (nodeName.equals(childNode.getNodeName())) {
                return childNode;
            }
            childNode = getChildNode(childNode, nodeName);
            if (childNode != null) {
                return childNode;
            }
        }
        return null;
    }
```

​		程序中MathTypeToLatex.mathTypeTransformer.Convert(new GoString.ByValue(path))是java调用go程序，此时发现每个mathType二进制文件java通过http调用go解析成latex耗时，此时需将go语言交叉编译为.so文件使用java jni调用，减少程序HTTP请求调用

### go交叉编译java可执行.so文件

​		交叉编译环境准备，如果不存在gcc环境，则安装gcc环境

```go
gcc -v	
```

​		提取go转mateType程序为latex程序代码MathTypeConvert.go，需要编译成动态库，也要有main函数，上面的`import "C"`一定要有 而且一定要有注释，java JNI调用的是C(C++)，方法上面需要有//export 注释

```go
// +build linux,amd64,go1.15,!cgo
package main

import (
	"C"
	"bytes"
	"container/list"
	"encoding/binary"
	"fmt"
	"github.com/extrame/ole2"
	"io"
	"io/ioutil"
	"log"
)

func main() {

}
//export Convert
func Convert(filepath string) *C.char {
	buffer, err := ioutil.ReadFile(filepath)
	if err != nil {
		fmt.Print(err)
	}

	reader := bytes.NewReader(buffer)
	mtef, err := Open(reader)
	if err != nil {
		fmt.Println(err)
	}

	latex := mtef.Translate()
	return C.CString(latex)
}

var SpecialChar = map[string]string{
	"!": "\\!",
	"#": "\\#",
	"$": "\\$",
	"%": "\\%",
	"&": "\\&",
	":": "\\:",
	";": "\\;",
	"_": "\\_",
	"{": "\\{",
	"|": "\\|",
	"}": "\\}",
}

var Chars = map[string]string{
	"char/0x0020":          " ",
	"char/0x0021":          "!",
	"char/0x0022":          "''",
	"char/0x0023/mathmode": "\\@#",
	"char/0x0024/mathmode": "\\$",
	"char/0x0025/mathmode": "%",
	"char/0x0026/mathmode": "\\And ",
	"char/0x0027/mathmode": "'",
	"char/0x0028":          "(",
	"char/0x0029":          ")",
	"char/0x002a":          "*",
	"char/0x002b":          "+",
	"char/0x002c":          ",",
	"char/0x002d":          "-",
	"char/0x002e":          ".",
	"char/0x002f":          "/",
	"char/0x0030":          "0",
	"char/0x0031":          "1",
	"char/0x0032":          "2",
	"char/0x0033":          "3",
	"char/0x0034":          "4",
	"char/0x0035":          "5",
	"char/0x0036":          "6",
	"char/0x0037":          "7",
	"char/0x0038":          "8",
	"char/0x0039":          "9",
	"char/0x003a":          ":",
	"char/0x003b":          ";",
	"char/0x003c/mathmode": " < ",
	"char/0x003d":          "=",
	"char/0x003e/mathmode": ">",
	"char/0x003f":          "?",
	"char/0x0041":          "A",
	"char/0x0042":          "B",
	"char/0x0043":          "C",
	"char/0x0044":          "D",
	"char/0x0045":          "E",
	"char/0x0046":          "F",
	"char/0x0047":          "G",
	"char/0x0048":          "H",
	"char/0x0049":          "I",
	"char/0x004a":          "J",
	"char/0x004b":          "K",
	"char/0x004c":          "L",
	"char/0x004d":          "M",
	"char/0x004e":          "N",
	"char/0x004f":          "O",
	"char/0x0050":          "P",
	"char/0x0051":          "Q",
	"char/0x0052":          "R",
	"char/0x0053":          "S",
	"char/0x0054":          "T",
	"char/0x0055":          "U",
	"char/0x0056":          "V",
	"char/0x0057":          "W",
	"char/0x0058":          "X",
	"char/0x0059":          "Y",
	"char/0x005a":          "Z",
	"char/0x005b/mathmode": "[",
	"char/0x005c/mathmode": "\\backslash ",
	"char/0x005d/mathmode": "]",
	"char/0x005e/mathmode": "\\hat{\\ }",
	"char/0x005f/mathmode": "\\_",
	"char/0x0060/mathmode": "\\grave{\\ }",
	"char/0x0061":          "a",
	"char/0x0062":          "b",
	"char/0x0063":          "c",
	"char/0x0064":          "d",
	"char/0x0065":          "e",
	"char/0x0066":          "f",
	"char/0x0067":          "g",
	"char/0x0068":          "h",
	"char/0x0069":          "i",
	"char/0x006a":          "j",
	"char/0x006b":          "k",
	"char/0x006c":          "l",
	"char/0x006d":          "m",
	"char/0x006e":          "n",
	"char/0x006f":          "o",
	"char/0x0070":          "p",
	"char/0x0071":          "q",
	"char/0x0072":          "r",
	"char/0x0073":          "s",
	"char/0x0074":          "t",
	"char/0x0075":          "u",
	"char/0x0076":          "v",
	"char/0x0077":          "w",
	"char/0x0078":          "x",
	"char/0x0079":          "y",
	"char/0x007a":          "z",
	"char/0x007b/mathmode": "\\{",
	"char/0x007c/mathmode": "|",
	"char/0x007d/mathmode": "\\}",
	"char/0x007e/mathmode": "\\tilde{\\ }",
	"char/0x00a0/mathmode": "~",
	"char/0x00a1/mathmode": "\\text{�}",
	"char/0x00a1/textmode": "�",
	"char/0x00a2/mathmode": "\\text{�}",
	"char/0x00a2/textmode": "�",
	"char/0x00a3/mathmode": "\\text{�}",
	"char/0x00a3/textmode": "�",
	"char/0x00a4/mathmode": "\\text{�}",
	"char/0x00a4/textmode": "�",
	"char/0x00a5/mathmode": "\\text{�}",
	"char/0x00a5/textmode": "�",
	"char/0x00a6/mathmode": "\\text{�}",
	"char/0x00a6/textmode": "�",
	"char/0x00a7/mathmode": "\\S ",
	"char/0x00a8/mathmode": "\\ddot{\\ }",
	"char/0x00aa/mathmode": "{}^\\text{a}",
	"char/0x00ab/mathmode": "\\ll ",
	"char/0x00ac/mathmode": "\\neg ",
	"char/0x00af/mathmode": "\\bar{\\ }",
	"char/0x00b0/mathmode": "{}^\\circ ",
	"char/0x00b1/mathmode": "\\pm ",
	"char/0x00b2/mathmode": "{}^\\text{2}",
	"char/0x00b3/mathmode": "{}^\\text{3}",
	"char/0x00b4/mathmode": "\\acute{\\ }",
	"char/0x00b5/mathmode": "\\mu ",
	"char/0x00b6/mathmode": "\\P ",
	"char/0x00b7/mathmode": "\\cdot ",
	"char/0x00b9/mathmode": "{}^\\text{1}",
	"char/0x00ba/mathmode": "{}^\\text{o}",
	"char/0x00bb/mathmode": "\\gg ",
	"char/0x00bc/mathmode": "{\\scriptscriptstyle 1\\!/\\!{ }_4}",
	"char/0x00bd/mathmode": "{\\scriptscriptstyle 1\\!/\\!{ }_2}",
	"char/0x00be/mathmode": "{\\scriptscriptstyle 3\\!/\\!{ }_4}",
	"char/0x00c0/mathmode": "\\grave{A}",
	"char/0x00c1/mathmode": "\\acute{A}",
	"char/0x00c2/mathmode": "\\hat{A}",
	"char/0x00c3/mathmode": "\\tilde{A}",
	"char/0x00c4/mathmode": "\\ddot{A}",
	"char/0x00c5/mathmode": "{\\AA}",
	"char/0x00c8/mathmode": "\\grave{E}",
	"char/0x00c9/mathmode": "\\acute{E}",
	"char/0x00ca/mathmode": "\\hat{E}",
	"char/0x00cb/mathmode": "\\ddot{E}",
	"char/0x00cc/mathmode": "\\grave{I}",
	"char/0x00cd/mathmode": "\\acute{I}",
	"char/0x00ce/mathmode": "\\hat{I}",
	"char/0x00cf/mathmode": "\\ddot{I}",
	"char/0x00d1/mathmode": "\\tilde{N}",
	"char/0x00d2/mathmode": "\\grave{O}",
	"char/0x00d3/mathmode": "\\acute{O}",
	"char/0x00d4/mathmode": "\\hat{O}",
	"char/0x00d5/mathmode": "\\tilde{O}",
	"char/0x00d6/mathmode": "\\ddot{O}",
	"char/0x00d7/mathmode": "\\times ",
	"char/0x00d8/mathmode": "\\O ",
	"char/0x00d9/mathmode": "\\grave{U}",
	"char/0x00da/mathmode": "\\acute{U}",
	"char/0x00db/mathmode": "\\hat{U}",
	"char/0x00dc/mathmode": "\\ddot{U}",
	"char/0x00dd/mathmode": "\\acute{Y}",
	"char/0x00e0/mathmode": "\\grave{a}",
	"char/0x00e1/mathmode": "\\acute{a}",
	"char/0x00e2/mathmode": "\\hat{a}",
	"char/0x00e3/mathmode": "\\tilde{a}",
	"char/0x00e4/mathmode": "\\ddot{a}",
	"char/0x00e8/mathmode": "\\grave{e}",
	"char/0x00e9/mathmode": "\\acute{e}",
	"char/0x00ea/mathmode": "\\hat{e}",
	"char/0x00eb/mathmode": "\\ddot{e}",
	"char/0x00ec/mathmode": "\\grave{i}",
	"char/0x00ed/mathmode": "\\acute{i}",
	"char/0x00ee/mathmode": "\\hat{i}",
	"char/0x00ef/mathmode": "\\ddot{i}",
	"char/0x00f0/mathmode": "\\eth ",
	"char/0x00f1/mathmode": "\\tilde{n}",
	"char/0x00f2/mathmode": "\\grave{o}",
	"char/0x00f3/mathmode": "\\acute{o}",
	"char/0x00f4/mathmode": "\\hat{o}",
	"char/0x00f5/mathmode": "\\tilde{o}",
	"char/0x00f6/mathmode": "\\ddot{o}",
	"char/0x00f7/mathmode": "\\div ",
	"char/0x00f9/mathmode": "\\grave{u}",
	"char/0x00fa/mathmode": "\\acute{u}",
	"char/0x00fb/mathmode": "\\hat{u}",
	"char/0x00fc/mathmode": "\\ddot{u}",
	"char/0x00fd/mathmode": "\\acute{y}",
	"char/0x00ff/mathmode": "\\ddot{y}",
	"char/0x0100/mathmode": "\\bar{A}",
	"char/0x0101/mathmode": "\\bar{a}",
	"char/0x0102/mathmode": "\\breve{A}",
	"char/0x0103/mathmode": "\\breve{a}",
	"char/0x0106/mathmode": "\\acute{C}",
	"char/0x0107/mathmode": "\\acute{c}",
	"char/0x0108/mathmode": "\\hat{C}",
	"char/0x0109/mathmode": "\\hat{c}",
	"char/0x010a/mathmode": "\\dot{C}",
	"char/0x010b/mathmode": "\\dot{c}",
	"char/0x010c/mathmode": "\\check{C}",
	"char/0x010d/mathmode": "\\check{c}",
	"char/0x010e/mathmode": "\\check{D}",
	"char/0x010f/mathmode": "\\check{d}",
	"char/0x0112/mathmode": "\\bar{E}",
	"char/0x0113/mathmode": "\\bar{e}",
	"char/0x0114/mathmode": "\\breve{E}",
	"char/0x0115/mathmode": "\\breve{e}",
	"char/0x0116/mathmode": "\\dot{E}",
	"char/0x0117/mathmode": "\\dot{e}",
	"char/0x011a/mathmode": "\\check{E}",
	"char/0x011b/mathmode": "\\check{e}",
	"char/0x011c/mathmode": "\\hat{G}",
	"char/0x011d/mathmode": "\\hat{g}",
	"char/0x011e/mathmode": "\\breve{G}",
	"char/0x011f/mathmode": "\\breve{g}",
	"char/0x0120/mathmode": "\\dot{G}",
	"char/0x0121/mathmode": "\\dot{g}",
	"char/0x0124/mathmode": "\\hat{H}",
	"char/0x0125/mathmode": "\\hat{h}",
	"char/0x0127/mathmode": "\\hbar ",
	"char/0x0128/mathmode": "\\tilde{I}",
	"char/0x0129/mathmode": "\\tilde{i}",
	"char/0x012a/mathmode": "\\bar{I}",
	"char/0x012b/mathmode": "\\bar{i}",
	"char/0x012c/mathmode": "\\breve{I}",
	"char/0x012d/mathmode": "\\breve{i}",
	"char/0x0130/mathmode": "\\dot{I}",
	"char/0x0131/mathmode": "\\imath ",
	"char/0x0134/mathmode": "\\hat{J}",
	"char/0x0135/mathmode": "\\hat{j}",
	"char/0x0139/mathmode": "\\acute{L}",
	"char/0x013a/mathmode": "\\acute{l}",
	"char/0x013d/mathmode": "\\check{L}",
	"char/0x013e/mathmode": "\\check{l}",
	"char/0x013f/mathmode": "\\mathrm{L}\\!\\!\\cdot",
	"char/0x0140/mathmode": "\\mathrm{l}\\cdot",
	"char/0x0143/mathmode": "\\acute{N}",
	"char/0x0144/mathmode": "\\acute{n}",
	"char/0x0147/mathmode": "\\check{N}",
	"char/0x0148/mathmode": "\\check{n}",
	"char/0x014c/mathmode": "\\bar{O}",
	"char/0x014d/mathmode": "\\bar{o}",
	"char/0x014e/mathmode": "\\breve{O}",
	"char/0x014f/mathmode": "\\breve{o}",
	"char/0x0154/mathmode": "\\acute{R}",
	"char/0x0155/mathmode": "\\acute{r}",
	"char/0x0158/mathmode": "\\check{R}",
	"char/0x0159/mathmode": "\\check{r}",
	"char/0x015a/mathmode": "\\acute{S}",
	"char/0x015b/mathmode": "\\acute{s}",
	"char/0x015c/mathmode": "\\hat{S}",
	"char/0x015d/mathmode": "\\hat{s}",
	"char/0x0160/mathmode": "\\check{S}",
	"char/0x0161/mathmode": "\\check{s}",
	"char/0x0164/mathmode": "\\check{T}",
	"char/0x0165/mathmode": "\\check{t}",
	"char/0x0168/mathmode": "\\tilde{U}",
	"char/0x0169/mathmode": "\\tilde{u}",
	"char/0x016a/mathmode": "\\bar{U}",
	"char/0x016b/mathmode": "\\bar{u}",
	"char/0x016c/mathmode": "\\breve{U}",
	"char/0x016d/mathmode": "\\breve{u}",
	"char/0x016e/mathmode": "\\overset{\\circ}{U}",
	"char/0x016f/mathmode": "\\overset{\\circ}{u}",
	"char/0x0174/mathmode": "\\hat{W}",
	"char/0x0175/mathmode": "\\hat{w}",
	"char/0x0176/mathmode": "\\hat{Y}",
	"char/0x0177/mathmode": "\\hat{y}",
	"char/0x0178/mathmode": "\\ddot{Y}",
	"char/0x0179/mathmode": "\\acute{Z}",
	"char/0x017a/mathmode": "\\acute{z}",
	"char/0x017b/mathmode": "\\dot{Z}",
	"char/0x017c/mathmode": "\\dot{z}",
	"char/0x017d/mathmode": "\\check{Z}",
	"char/0x017e/mathmode": "\\check{z}",
	"char/0x0391/mathmode": "\\Alpha ",
	"char/0x0392/mathmode": "\\Beta ",
	"char/0x0393/mathmode": "\\Gamma ",
	"char/0x0394/mathmode": "\\Delta ",
	"char/0x0395/mathmode": "\\Epsilon ",
	"char/0x0396/mathmode": "\\Zeta ",
	"char/0x0397/mathmode": "\\Eta ",
	"char/0x0398/mathmode": "\\Theta ",
	"char/0x0399/mathmode": "\\Iota ",
	"char/0x039a/mathmode": "\\Kappa ",
	"char/0x039b/mathmode": "\\Lambda ",
	"char/0x039c/mathmode": "\\Mu ",
	"char/0x039d/mathmode": "\\Nu ",
	"char/0x039e/mathmode": "\\Xi ",
	"char/0x039f/mathmode": "{\\mathrm O}",
	"char/0x039f/textmode": "O",
	"char/0x03a0/mathmode": "\\Pi ",
	"char/0x03a1/mathmode": "\\Rho ",
	"char/0x03a3/mathmode": "\\Sigma ",
	"char/0x03a4/mathmode": "\\Tau ",
	"char/0x03a5/mathmode": "\\Upsilon ",
	"char/0x03a6/mathmode": "\\Phi ",
	"char/0x03a7/mathmode": "\\Chi ",
	"char/0x03a8/mathmode": "\\Psi ",
	"char/0x03a9/mathmode": "\\Omega ",
	"char/0x03b1/mathmode": "\\alpha ",
	"char/0x03b2/mathmode": "\\beta ",
	"char/0x03b3/mathmode": "\\gamma ",
	"char/0x03b4/mathmode": "\\delta ",
	"char/0x03b5/mathmode": "\\varepsilon ",
	"char/0x03b6/mathmode": "\\zeta ",
	"char/0x03b7/mathmode": "\\eta ",
	"char/0x03b8/mathmode": "\\theta ",
	"char/0x03b9/mathmode": "\\iota ",
	"char/0x03ba/mathmode": "\\kappa ",
	"char/0x03bb/mathmode": "\\lambda ",
	"char/0x03bc/mathmode": "\\mu ",
	"char/0x03bd/mathmode": "\\nu ",
	"char/0x03be/mathmode": "\\xi ",
	"char/0x03bf":          "o",
	"char/0x03c0/mathmode": "\\pi ",
	"char/0x03c1/mathmode": "\\rho ",
	"char/0x03c2/mathmode": "\\varsigma ",
	"char/0x03c3/mathmode": "\\sigma ",
	"char/0x03c4/mathmode": "\\tau ",
	"char/0x03c5/mathmode": "\\upsilon ",
	"char/0x03c6/mathmode": "\\varphi ",
	"char/0x03c7/mathmode": "\\chi ",
	"char/0x03c8/mathmode": "\\psi ",
	"char/0x03c9/mathmode": "\\omega ",
	"char/0x03d1/mathmode": "\\vartheta ",
	"char/0x03d2/mathmode": "\\Upsilon ",
	"char/0x03d5/mathmode": "\\phi ",
	"char/0x03d6/mathmode": "\\varpi ",
	"char/0x03db/mathmode": "\\varsigma ",
	"char/0x03dc/mathmode": "\\digamma ",
	"char/0x03f0/mathmode": "\\varkappa ",
	"char/0x03f1/mathmode": "\\varrho ",
	"char/0x03f5/mathmode": "\\epsilon ",
	"char/0x03f6/mathmode": "\\backepsilon ",
	"char/0x2010/textmode": "-",
	"char/0x2013/textmode": "--",
	"char/0x2014/textmode": "---",
	"char/0x2016/mathmode": "\\|",
	"char/0x2020/mathmode": "\\dagger ",
	"char/0x2021/mathmode": "\\ddagger ",
	"char/0x2022/mathmode": "\\bullet ",
	"char/0x2026/mathmode": "\\ldots ",
	"char/0x2032/mathmode": "\\prime ",
	"char/0x2033/mathmode": "\\prime\\prime ",
	"char/0x2034/mathmode": "\\prime\\prime\\prime ",
	"char/0x2035/mathmode": "\\backprime ",
	"char/0x2036/mathmode": "\\backprime\\backprime ",
	"char/0x2037/mathmode": "\\backprime\\backprime\\backprime ",
	"char/0x203e/mathmode": "\\bar{\\ }",
	"char/0x203f/mathmode": "{}_\\smile ",
	"char/0x2040/mathmode": "{}^\\frown ",
	"char/0x2044":          "/",
	"char/0x20a1/mathmode": "\\Colon ",
	"char/0x20a2/mathmode": "\\cruzeiro ",
	"char/0x20a3/mathmode": "\\franc ",
	"char/0x20a4/mathmode": "\\lira ",
	"char/0x20a5/mathmode": "\\mill ",
	"char/0x20a6/mathmode": "\\naira ",
	"char/0x20a7/mathmode": "\\peseta ",
	"char/0x20a8/mathmode": "\\rupee ",
	"char/0x20a9/mathmode": "\\won ",
	"char/0x20aa/mathmode": "\\newsheqel ",
	"char/0x20ab/mathmode": "\\dong ",
	"char/0x20ac/mathmode": "\\euro ",
	"char/0x20ad/mathmode": "\\kip ",
	"char/0x20ae/mathmode": "\\tugrik ",
	"char/0x20af/mathmode": "\\drachma ",
	"char/0x20b0/mathmode": "\\germanpenny ",
	"char/0x20b1/mathmode": "\\peso ",
	"char/0x20b2/mathmode": "\\guarani ",
	"char/0x20b3/mathmode": "\\austral ",
	"char/0x20b4/mathmode": "\\hryvnia ",
	"char/0x20b5/mathmode": "\\cedi ",
	"char/0x2102/mathmode": "\\mathbb{C}",
	"char/0x2107/mathmode": "\\gamma ",
	"char/0x210a":          "g",
	"char/0x210b/mathmode": "\\mathcal{H}",
	"char/0x210c/mathmode": "\\mathfrak{H}",
	"char/0x210d/mathmode": "\\mathbb{H}",
	"char/0x210e/mathmode": "h",
	"char/0x210f/mathmode": "\\hbar ",
	"char/0x2110/mathmode": "\\mathcal{I}",
	"char/0x2111/mathmode": "\\Im ",
	"char/0x2112/mathmode": "\\mathcal{L}",
	"char/0x2113/mathmode": "\\ell ",
	"char/0x2115/mathmode": "\\mathbb{N}",
	"char/0x2118/mathmode": "\\wp ",
	"char/0x2119/mathmode": "\\mathbb{P}",
	"char/0x211a/mathmode": "\\mathbb{Q}",
	"char/0x211b/mathmode": "\\mathcal{R}",
	"char/0x211c/mathmode": "\\Re ",
	"char/0x211d/mathmode": "\\mathbb{R}",
	"char/0x2124/mathmode": "\\mathbb{Z}",
	"char/0x2126/mathmode": "\\Omega ",
	"char/0x2127/mathmode": "\\mho ",
	"char/0x2128/mathmode": "\\mathfrak{X}",
	"char/0x212c/mathmode": "\\mathcal{B}",
	"char/0x212d/mathmode": "\\mathfrak{C}",
	"char/0x2130/mathmode": "\\mathcal{E}",
	"char/0x2131/mathmode": "\\mathcal{F}",
	"char/0x2132/mathmode": "\\Finv ",
	"char/0x2133/mathmode": "\\mathcal{M}",
	"char/0x2135/mathmode": "\\aleph ",
	"char/0x2136/mathmode": "\\beth ",
	"char/0x2137/mathmode": "\\gimel ",
	"char/0x2138/mathmode": "\\daleth ",
	"char/0x2190/mathmode": "\\leftarrow ",
	"char/0x2191/mathmode": "\\uparrow ",
	"char/0x2192/mathmode": "\\to ",
	"char/0x2193/mathmode": "\\downarrow ",
	"char/0x2194/mathmode": "\\leftrightarrow ",
	"char/0x2195/mathmode": "\\updownarrow ",
	"char/0x2196/mathmode": "\\nwarrow ",
	"char/0x2197/mathmode": "\\nearrow ",
	"char/0x2198/mathmode": "\\searrow ",
	"char/0x2199/mathmode": "\\swarrow ",
	"char/0x219a/mathmode": "\\nleftarrow ",
	"char/0x219b/mathmode": "\\nrightarrow ",
	"char/0x219d/mathmode": "\\rightsquigarrow ",
	"char/0x219e/mathmode": "\\twoheadleftarrow ",
	"char/0x21a0/mathmode": "\\twoheadrightarrow ",
	"char/0x21a2/mathmode": "\\leftarrowtail ",
	"char/0x21a3/mathmode": "\\rightarrowtail ",
	"char/0x21a6/mathmode": "\\mapsto ",
	"char/0x21a9/mathmode": "\\hookleftarrow ",
	"char/0x21aa/mathmode": "\\hookrightarrow ",
	"char/0x21ab/mathmode": "\\looparrowleft ",
	"char/0x21ac/mathmode": "\\looparrowright ",
	"char/0x21ad/mathmode": "\\leftrightsquigarrow ",
	"char/0x21ae/mathmode": "\\nleftrightarrow ",
	"char/0x21b0/mathmode": "\\Lsh ",
	"char/0x21b1/mathmode": "\\Rsh ",
	"char/0x21b6/mathmode": "\\curvearrowleft ",
	"char/0x21b7/mathmode": "\\curvearrowright ",
	"char/0x21ba/mathmode": "\\circlearrowleft ",
	"char/0x21bb/mathmode": "\\circlearrowright ",
	"char/0x21bc/mathmode": "\\leftharpoonup ",
	"char/0x21bd/mathmode": "\\leftharpoondown ",
	"char/0x21be/mathmode": "\\upharpoonright ",
	"char/0x21bf/mathmode": "\\upharpoonleft ",
	"char/0x21c0/mathmode": "\\rightharpoonup ",
	"char/0x21c1/mathmode": "\\rightharpoondown ",
	"char/0x21c2/mathmode": "\\downharpoonright ",
	"char/0x21c3/mathmode": "\\downharpoonleft ",
	"char/0x21c4/mathmode": "\\rightleftarrows ",
	"char/0x21c5/mathmode": "\\uparrow\\!\\downarrow ",
	"char/0x21c6/mathmode": "\\leftrightarrows ",
	"char/0x21c7/mathmode": "\\leftleftarrows ",
	"char/0x21c8/mathmode": "\\upuparrows ",
	"char/0x21c9/mathmode": "\\rightrightarrows ",
	"char/0x21ca/mathmode": "\\downdownarrows ",
	"char/0x21cb/mathmode": "\\leftrightharpoons ",
	"char/0x21cc/mathmode": "\\rightleftharpoons ",
	"char/0x21cd/mathmode": "\\nLeftarrow ",
	"char/0x21ce/mathmode": "\\nLeftrightarrow ",
	"char/0x21cf/mathmode": "\\nRightarrow ",
	"char/0x21d0/mathmode": "\\Leftarrow ",
	"char/0x21d1/mathmode": "\\Uparrow ",
	"char/0x21d2/mathmode": "\\Rightarrow ",
	"char/0x21d3/mathmode": "\\Downarrow ",
	"char/0x21d4/mathmode": "\\Leftrightarrow ",
	"char/0x21d5/mathmode": "\\Updownarrow ",
	"char/0x21da/mathmode": "\\Lleftarrow ",
	"char/0x21db/mathmode": "\\Rrightarrow ",
	"char/0x21dd/mathmode": "\\rightsquigarrow ",
	"char/0x21e4/mathmode": "\\shortmid\\!\\leftarrow ",
	"char/0x21e5/mathmode": "\\shortmid\\!\\rightarrow ",
	"char/0x2200/mathmode": "\\forall ",
	"char/0x2201/mathmode": "\\complement ",
	"char/0x2202/mathmode": "\\partial ",
	"char/0x2203/mathmode": "\\exists ",
	"char/0x2204/mathmode": "\\nexists ",
	"char/0x2205/mathmode": "\\varnothing ",
	"char/0x2206/mathmode": "\\Delta ",
	"char/0x2207/mathmode": "\\nabla ",
	"char/0x2208/mathmode": "\\in ",
	"char/0x2209/mathmode": "\\notin ",
	"char/0x220b/mathmode": "\\ni ",
	"char/0x220c/mathmode": "\\not\\ni ",
	"char/0x220d/mathmode": "\\backepsilon  ",
	"char/0x220e/mathmode": "\\blacksquare ",
	"char/0x220f/mathmode": "\\prod ",
	"char/0x2210/mathmode": "\\coprod ",
	"char/0x2211/mathmode": "\\sum ",
	"char/0x2212":          "-",
	"char/0x2213/mathmode": "\\mp ",
	"char/0x2214/mathmode": "\\dotplus ",
	"char/0x2215":          "/",
	"char/0x2216/mathmode": "\\setminus ",
	"char/0x2217":          "*",
	"char/0x2218/mathmode": "\\circ ",
	"char/0x2219/mathmode": "\\bullet ",
	"char/0x221a/mathmode": "\\surd ",
	"char/0x221b/mathmode": "\\sqrt[3]{ } ",
	"char/0x221c/mathmode": "\\sqrt[4]{ } ",
	"char/0x221d/mathmode": "\\propto ",
	"char/0x221e/mathmode": "\\infty ",
	"char/0x221f/mathmode": "\\perp ",
	"char/0x2220/mathmode": "\\angle ",
	"char/0x2221/mathmode": "\\measuredangle ",
	"char/0x2222/mathmode": "\\sphericalangle ",
	"char/0x2223/mathmode": "\\mid ",
	"char/0x2224/mathmode": "\\nmid ",
	"char/0x2225/mathmode": "\\parallel ",
	"char/0x2226/mathmode": "\\nparallel ",
	"char/0x2227/mathmode": "\\wedge ",
	"char/0x2228/mathmode": "\\vee ",
	"char/0x2229/mathmode": "\\cap ",
	"char/0x222a/mathmode": "\\cup ",
	"char/0x222b/mathmode": "\\int ",
	"char/0x222c/mathmode": "\\iint ",
	"char/0x222d/mathmode": "\\iiint ",
	"char/0x222e/mathmode": "\\oint ",
	"char/0x222f/mathmode": "\\mathop{{\\int\\!\\!\\!\\!\\!\\int}\\!\\!\\!\\!\\!\\!\\!\\bigcirc}",
	"char/0x2230/mathmode": "\\mathop{{\\int\\!\\!\\!\\!\\!\\int\\!\\!\\!\\!\\!\\int}\\!\\!\\!\\!\\!\\!\\!\\!\\bigcirc}",
	"char/0x2231/mathmode": "\\mathop{\\int\\!\\!\\!\\!\\!\\!\\!\\curvearrowright}",
	"char/0x2232/mathmode": "\\mathop{\\int\\!\\!\\!\\!\\!\\!\\!\\circlearrowright}",
	"char/0x2233/mathmode": "\\mathop{\\int\\!\\!\\!\\!\\!\\!\\!\\circlearrowleft}",
	"char/0x2234/mathmode": "\\therefore ",
	"char/0x2235/mathmode": "\\because ",
	"char/0x2236/mathmode": "\\,\\!:",
	"char/0x2236/textmode": ":",
	"char/0x2237/mathmode": "\\,\\!:\\,:",
	"char/0x2237/textmode": "::",
	"char/0x2238/mathmode": "\\dot-",
	"char/0x2239/mathmode": "\\,-\\!:",
	"char/0x223a/mathmode": ":\\!\\!\\!-\\!\\!\\!:",
	"char/0x223b/mathmode": "\\sim\\!\\!\\!:\\,",
	"char/0x223c/mathmode": "\\sim ",
	"char/0x223d/mathmode": "\\backsim ",
	"char/0x2240/mathmode": "\\wr ",
	"char/0x2241/mathmode": "\\nsim ",
	"char/0x2242/mathmode": "\\eqsim ",
	"char/0x2243/mathmode": "\\simeq ",
	"char/0x2245/mathmode": "\\cong ",
	"char/0x2247/mathmode": "\\ncong ",
	"char/0x2248/mathmode": "\\approx ",
	"char/0x2249/mathmode": "\\not\\approx ",
	"char/0x224a/mathmode": "\\approxeq ",
	"char/0x224d/mathmode": "\\asymp ",
	"char/0x224e/mathmode": "\\Bumpeq ",
	"char/0x224f/mathmode": "\\bumpeq ",
	"char/0x2250/mathmode": "\\doteq ",
	"char/0x2251/mathmode": "\\doteqdot ",
	"char/0x2252/mathmode": "\\fallingdotseq ",
	"char/0x2253/mathmode": "\\risingdotseq ",
	"char/0x2254/mathmode": ":=\\,\\!",
	"char/0x2254/textmode": ":=",
	"char/0x2255/mathmode": "=:\\,\\!",
	"char/0x2255/textmode": "=:",
	"char/0x2256/mathmode": "\\eqcirc ",
	"char/0x2257/mathmode": "\\circeq ",
	"char/0x2258/mathmode": "\\overset{\\frown}{=}",
	"char/0x2259/mathmode": "\\overset{\\wedge}{=}",
	"char/0x225a/mathmode": "\\overset{\\vee}{=}",
	"char/0x225b/mathmode": "\\overset{\\star}{=}",
	"char/0x225c/mathmode": "\\triangleq ",
	"char/0x225d/mathmode": "\\overset{\\underset{\\mathrm{def}}{}}{=}",
	"char/0x225e/mathmode": "\\overset{\\underset{\\mathrm{m}}{}}{=}",
	"char/0x225f/mathmode": "\\overset{?}{=}",
	"char/0x2260/mathmode": "\\ne ",
	"char/0x2261/mathmode": "\\equiv ",
	"char/0x2262/mathmode": "\\not\\equiv ",
	"char/0x2264/mathmode": "\\le ",
	"char/0x2265/mathmode": "\\ge ",
	"char/0x2266/mathmode": "\\leqq ",
	"char/0x2267/mathmode": "\\geqq ",
	"char/0x2268/mathmode": "\\lneqq ",
	"char/0x2269/mathmode": "\\gneqq ",
	"char/0x226a/mathmode": "\\ll ",
	"char/0x226b/mathmode": "\\gg ",
	"char/0x226c/mathmode": "\\between ",
	"char/0x226d/mathmode": "\\not\\asymp ",
	"char/0x226e/mathmode": "\\nless ",
	"char/0x226f/mathmode": "\\ngtr ",
	"char/0x2270/mathmode": "\\nleqslant ",
	"char/0x2271/mathmode": "\\ngeqslant ",
	"char/0x2272/mathmode": "\\lesssim ",
	"char/0x2273/mathmode": "\\gtrsim ",
	"char/0x2274/mathmode": "\\not\\lesssim ",
	"char/0x2275/mathmode": "\\not\\gtrsim ",
	"char/0x2276/mathmode": "\\lessgtr ",
	"char/0x2277/mathmode": "\\gtrless ",
	"char/0x2278/mathmode": "\\not\\lessgtr ",
	"char/0x2279/mathmode": "\\not\\gtrless ",
	"char/0x227a/mathmode": "\\prec ",
	"char/0x227b/mathmode": "\\succ ",
	"char/0x227c/mathmode": "\\preccurlyeq ",
	"char/0x227d/mathmode": "\\succcurlyeq ",
	"char/0x227e/mathmode": "\\precsim ",
	"char/0x227f/mathmode": "\\succsim ",
	"char/0x2280/mathmode": "\\nprec ",
	"char/0x2281/mathmode": "\\nsucc ",
	"char/0x2282/mathmode": "\\subset ",
	"char/0x2283/mathmode": "\\supset ",
	"char/0x2284/mathmode": "\\not\\subset ",
	"char/0x2285/mathmode": "\\not\\supset ",
	"char/0x2286/mathmode": "\\subseteq ",
	"char/0x2287/mathmode": "\\supseteq ",
	"char/0x2288/mathmode": "\\nsubseteq ",
	"char/0x2289/mathmode": "\\nsupseteq ",
	"char/0x228a/mathmode": "\\subsetneq ",
	"char/0x228b/mathmode": "\\supsetneq ",
	"char/0x228e/mathmode": "\\uplus ",
	"char/0x228f/mathmode": "\\sqsubset ",
	"char/0x2290/mathmode": "\\sqsupset ",
	"char/0x2291/mathmode": "\\sqsubseteq ",
	"char/0x2292/mathmode": "\\sqsupseteq ",
	"char/0x2293/mathmode": "\\sqcap ",
	"char/0x2294/mathmode": "\\sqcup ",
	"char/0x2295/mathmode": "\\oplus ",
	"char/0x2296/mathmode": "\\ominus ",
	"char/0x2297/mathmode": "\\otimes ",
	"char/0x2298/mathmode": "\\oslash ",
	"char/0x2299/mathmode": "\\odot ",
	"char/0x229a/mathmode": "\\circledcirc ",
	"char/0x229b/mathmode": "\\circledast ",
	"char/0x229c/mathmode": "\\,\\bigcirc\\!\\!\\!\\!\\!\\!\\!=",
	"char/0x229d/mathmode": "\\circleddash ",
	"char/0x229e/mathmode": "\\boxplus ",
	"char/0x229f/mathmode": "\\boxminus ",
	"char/0x22a0/mathmode": "\\boxtimes ",
	"char/0x22a1/mathmode": "\\boxdot ",
	"char/0x22a2/mathmode": "\\vdash ",
	"char/0x22a3/mathmode": "\\dashv ",
	"char/0x22a4/mathmode": "\\top ",
	"char/0x22a5/mathmode": "\\bot ",
	"char/0x22a6/mathmode": "\\vdash ",
	"char/0x22a7/mathmode": "\\models ",
	"char/0x22a8/mathmode": "\\vDash ",
	"char/0x22a9/mathmode": "\\Vdash ",
	"char/0x22aa/mathmode": "\\Vvdash ",
	"char/0x22ab/mathmode": "\\,\\|\\!\\!\\!=",
	"char/0x22ac/mathmode": "\\nvdash ",
	"char/0x22ad/mathmode": "\\nvDash ",
	"char/0x22ae/mathmode": "\\nVdash ",
	"char/0x22af/mathmode": "\\nVDash ",
	"char/0x22b2/mathmode": "\\triangleleft ",
	"char/0x22b3/mathmode": "\\triangleright ",
	"char/0x22b4/mathmode": "\\trianglelefteq ",
	"char/0x22b5/mathmode": "\\trianglerighteq ",
	"char/0x22b8/mathmode": "\\multimap ",
	"char/0x22ba/mathmode": "\\intercal ",
	"char/0x22bb/mathmode": "\\veebar ",
	"char/0x22bc/mathmode": "\\barwedge ",
	"char/0x22bd/mathmode": "\\veebar ",
	"char/0x22c0/mathmode": "\\bigwedge ",
	"char/0x22c1/mathmode": "\\bigvee ",
	"char/0x22c2/mathmode": "\\bigcap ",
	"char/0x22c3/mathmode": "\\bigcup ",
	"char/0x22c4/mathmode": "\\diamond ",
	"char/0x22c5/mathmode": "\\cdot ",
	"char/0x22c6/mathmode": "\\star ",
	"char/0x22c7/mathmode": "\\divideontimes ",
	"char/0x22c8/mathmode": "\\bowtie ",
	"char/0x22c9/mathmode": "\\ltimes ",
	"char/0x22ca/mathmode": "\\rtimes ",
	"char/0x22cb/mathmode": "\\leftthreetimes ",
	"char/0x22cc/mathmode": "\\rightthreetimes ",
	"char/0x22ce/mathmode": "\\curlyvee ",
	"char/0x22cf/mathmode": "\\curlywedge ",
	"char/0x22d0/mathmode": "\\Subset ",
	"char/0x22d1/mathmode": "\\Supset ",
	"char/0x22d2/mathmode": "\\Cap ",
	"char/0x22d3/mathmode": "\\Cup ",
	"char/0x22d4/mathmode": "\\pitchfork ",
	"char/0x22d6/mathmode": "\\lessdot ",
	"char/0x22d7/mathmode": "\\gtrdot ",
	"char/0x22d8/mathmode": "\\lll ",
	"char/0x22d9/mathmode": "\\ggg ",
	"char/0x22da/mathmode": "\\lesseqgtr ",
	"char/0x22db/mathmode": "\\gtreqless ",
	"char/0x22dc/mathmode": "\\eqslantless ",
	"char/0x22dd/mathmode": "\\eqslantgtr ",
	"char/0x22de/mathmode": "\\curlyeqprec ",
	"char/0x22df/mathmode": "\\curlyeqsucc ",
	"char/0x22e0/mathmode": "\\npreceq ",
	"char/0x22e1/mathmode": "\\nsucceq ",
	"char/0x22e6/mathmode": "\\lnsim ",
	"char/0x22e7/mathmode": "\\gnsim ",
	"char/0x22e8/mathmode": "\\precnsim ",
	"char/0x22e9/mathmode": "\\succnsim ",
	"char/0x22ea/mathmode": "\\ntriangleleft ",
	"char/0x22eb/mathmode": "\\ntriangleright ",
	"char/0x22ec/mathmode": "\\ntrianglelefteq ",
	"char/0x22ed/mathmode": "\\ntrianglerighteq ",
	"char/0x22ee/mathmode": "\\vdots ",
	"char/0x22ef/mathmode": "\\cdots ",
	"char/0x22f1/mathmode": "\\ddots ",
	"char/0x2300/mathmode": "\\varnothing ",
	"char/0x2306/mathmode": "\\doublebarwedge ",
	"char/0x2308/mathmode": "\\lceil ",
	"char/0x2309/mathmode": "\\rceil ",
	"char/0x230a/mathmode": "\\lfloor ",
	"char/0x230b/mathmode": "\\rfloor ",
	"char/0x231c/mathmode": "\\ulcorner ",
	"char/0x231d/mathmode": "\\urcorner ",
	"char/0x231e/mathmode": "\\llcorner ",
	"char/0x231f/mathmode": "\\lrcorner ",
	"char/0x2322/mathmode": "\\frown ",
	"char/0x2323/mathmode": "\\smile ",
	"char/0x2329/mathmode": "\\langle ",
	"char/0x232a/mathmode": "\\rangle ",
	"char/0x2329":          "\\langle ",
	"char/0x232a":          "\\rangle ",
	"char/0x2353/mathmode": "\\Box\\!\\!\\!\\!\\wedge",
	"char/0x2370/mathmode": "\\Box\\!\\!\\!\\!?",
	"char/0x2423/mathmode": "\\textvisiblespace ",
	"char/0x24c8/mathmode": "\\circledS ",
	"char/0x25a0/mathmode": "\\blacksquare ",
	"char/0x25a1/mathmode": "\\square ",
	"char/0x25b2/mathmode": "\\blacktriangle ",
	"char/0x25b3/mathmode": "\\vartriangle ",
	"char/0x25b6/mathmode": "\\blacktriangleright ",
	"char/0x25bc/mathmode": "\\blacktriangledown ",
	"char/0x25bd/mathmode": "\\triangledown ",
	"char/0x25c0/mathmode": "\\blacktriangleleft ",
	"char/0x25ca/mathmode": "\\lozenge ",
	"char/0x25cb/mathmode": "\\bigcirc ",
	"char/0x2605/mathmode": "\\bigstar ",
	"char/0x2660/mathmode": "\\spadesuit ",
	"char/0x2661/mathmode": "\\heartsuit ",
	"char/0x2662/mathmode": "\\diamondsuit ",
	"char/0x2663/mathmode": "\\clubsuit ",
	"char/0x2666/mathmode": "\\blacklozenge ",
	"char/0x266d/mathmode": "\\flat ",
	"char/0x266e/mathmode": "\\natural ",
	"char/0x266f/mathmode": "\\sharp ",
	"char/0x2713/mathmode": "\\checkmark ",
	"char/0x2715/mathmode": "\\times ",
	"char/0x27e6/mathmode": "[\\![",
	"char/0x27e7/mathmode": "]\\!]",
	"char/0x27e8/mathmode": "\\langle ",
	"char/0x27e9/mathmode": "\\rangle ",
	"char/0x27f5/mathmode": "\\longleftarrow ",
	"char/0x27f6/mathmode": "\\longrightarrow ",
	"char/0x27f7/mathmode": "\\longleftrightarrow ",
	"char/0x27f8/mathmode": "\\Longleftarrow ",
	"char/0x27f9/mathmode": "\\Longrightarrow ",
	"char/0x27fa/mathmode": "\\Longleftrightarrow ",
	"char/0x27fc/mathmode": "\\longmapsto ",
	"char/0x29eb/mathmode": "\\blacklozenge ",
	"char/0x29f8/mathmode": "\\Big /",
	"char/0x29f9/mathmode": "\\Big\\backslash ",
	"char/0x2a00/mathmode": "\\bigodot ",
	"char/0x2a01/mathmode": "\\bigoplus ",
	"char/0x2a02/mathmode": "\\bigotimes ",
	"char/0x2a04/mathmode": "\\biguplus ",
	"char/0x2a06/mathmode": "\\bigsqcup ",
	"char/0x2a0c/mathmode": "\\iiiint ",
	"char/0x3008/mathmode": "\\langle ",
	"char/0x3009/mathmode": "\\rangle ",
	"char/0x301a/mathmode": "[\\![",
	"char/0x301b/mathmode": "]\\!]",
	"char/0xe90b/mathmode": "\\supseteqq ",
	"char/0xe90c/mathmode": "\\subseteqq ",
	"char/0xe922/mathmode": "\\lesseqqgtr ",
	"char/0xe92d/mathmode": "\\gtreqqless ",
	"char/0xe92e/mathmode": "\\shortmid ",
	"char/0xe92f/mathmode": "\\shortparallel ",
	"char/0xe930/mathmode": "\\leqslant ",
	"char/0xe931/mathmode": "\\geqslant ",
	"char/0xe932/mathmode": "\\lessapprox ",
	"char/0xe933/mathmode": "\\gtrapprox ",
	"char/0xe938/mathmode": "\\preceq ",
	"char/0xe939/mathmode": "\\succeq ",
	"char/0xe93a/mathmode": "\\precapprox ",
	"char/0xe93b/mathmode": "\\succapprox ",
	"char/0xe981/mathmode": "\\circleddash ",
	"char/0xe98f/mathmode": "\\centerdot ",
	"char/0xea06/mathmode": "\\nleq ",
	"char/0xea07/mathmode": "\\ngeq ",
	"char/0xea0b/mathmode": "\\nsupseteqq ",
	"char/0xea0c/mathmode": "\\nsubseteqq ",
	"char/0xea0e/mathmode": "\\not\\eqslantgtr ",
	"char/0xea0f/mathmode": "\\not\\eqsim ",
	"char/0xea10/mathmode": "\\not\\eqslantless ",
	"char/0xea11/mathmode": "\\nsim ",
	"char/0xea2e/mathmode": "\\nshortmid ",
	"char/0xea2f/mathmode": "\\nshortparallel ",
	"char/0xea32/mathmode": "\\lnapprox ",
	"char/0xea33/mathmode": "\\gnapprox ",
	"char/0xea34/mathmode": "\\lneq ",
	"char/0xea35/mathmode": "\\gneq ",
	"char/0xea38/mathmode": "\\npreceq ",
	"char/0xea39/mathmode": "\\nsucceq ",
	"char/0xea3a/mathmode": "\\precnapprox ",
	"char/0xea3b/mathmode": "\\succnapprox ",
	"char/0xea40/mathmode": "\\precneqq ",
	"char/0xea41/mathmode": "\\succneqq ",
	"char/0xea42/mathmode": "\\varsubsetneq ",
	"char/0xea43/mathmode": "\\varsupsetneq ",
	"char/0xea44/mathmode": "\\subsetneqq ",
	"char/0xea45/mathmode": "\\supsetneqq ",
	"char/0xea46/mathmode": "\\varsubsetneqq ",
	"char/0xea47/mathmode": "\\varsupsetneqq ",
	"char/0xeb01/mathmode": "\\overrightarrow{\\scriptscriptstyle\\leftarrow}",
	"char/0xeb02/mathmode": "\\stackrel{\\scriptscriptstyle\\to}{\\leftarrow}",
	"char/0xed01/mathmode": "\\Game ",
	"char/0xed02/mathmode": "\\jmath ",
	"char/0xed10":          " d ",
	"char/0xed11":          " e ",
	"char/0xed12":          " i ",
	"char/0xed13":          " j ",
	"char/0xed16":          " D ",
	"char/0xef00/mathmode": " & ",
	"char/0xef01":          "",
	"char/0x200b":          "",
	"char/0xef02/mathmode": "\\,",
	"char/0x2009/mathmode": "\\,",
	"char/0xef03/mathmode": "\\;",
	"char/0x205f/mathmode": "\\;",
	"char/0xef04/mathmode": "\\ ",
	"char/0x2004/mathmode": "\\ ",
	"char/0xef05/mathmode": "\\quad ",
	"char/0x2003/mathmode": "\\quad ",
	"char/0xef06/mathmode": "\\qquad ",
	"char/0xef22/mathmode": "\\!",
	"char/0xf000/mathmode": "\\mathfrak{A}",
	"char/0xf001/mathmode": "\\mathfrak{B}",
	"char/0xf002/mathmode": "\\mathfrak{C}",
	"char/0xf003/mathmode": "\\mathfrak{D}",
	"char/0xf004/mathmode": "\\mathfrak{E}",
	"char/0xf005/mathmode": "\\mathfrak{F}",
	"char/0xf006/mathmode": "\\mathfrak{G}",
	"char/0xf007/mathmode": "\\mathfrak{H}",
	"char/0xf008/mathmode": "\\mathfrak{I}",
	"char/0xf009/mathmode": "\\mathfrak{J}",
	"char/0xf00a/mathmode": "\\mathfrak{K}",
	"char/0xf00b/mathmode": "\\mathfrak{L}",
	"char/0xf00c/mathmode": "\\mathfrak{M}",
	"char/0xf00d/mathmode": "\\mathfrak{N}",
	"char/0xf00e/mathmode": "\\mathfrak{O}",
	"char/0xf00f/mathmode": "\\mathfrak{P}",
	"char/0xf010/mathmode": "\\mathfrak{Q}",
	"char/0xf011/mathmode": "\\mathfrak{R}",
	"char/0xf012/mathmode": "\\mathfrak{S}",
	"char/0xf013/mathmode": "\\mathfrak{T}",
	"char/0xf014/mathmode": "\\mathfrak{U}",
	"char/0xf015/mathmode": "\\mathfrak{V}",
	"char/0xf016/mathmode": "\\mathfrak{W}",
	"char/0xf017/mathmode": "\\mathfrak{X}",
	"char/0xf018/mathmode": "\\mathfrak{Y}",
	"char/0xf019/mathmode": "\\mathfrak{Z}",
	"char/0xf01a/mathmode": "\\mathfrak{a}",
	"char/0xf01b/mathmode": "\\mathfrak{b}",
	"char/0xf01c/mathmode": "\\mathfrak{c}",
	"char/0xf01d/mathmode": "\\mathfrak{d}",
	"char/0xf01e/mathmode": "\\mathfrak{e}",
	"char/0xf01f/mathmode": "\\mathfrak{f}",
	"char/0xf020/mathmode": "\\mathfrak{g}",
	"char/0xf021/mathmode": "\\mathfrak{h}",
	"char/0xf022/mathmode": "\\mathfrak{i}",
	"char/0xf023/mathmode": "\\mathfrak{j}",
	"char/0xf024/mathmode": "\\mathfrak{k}",
	"char/0xf025/mathmode": "\\mathfrak{l}",
	"char/0xf026/mathmode": "\\mathfrak{m}",
	"char/0xf027/mathmode": "\\mathfrak{n}",
	"char/0xf028/mathmode": "\\mathfrak{o}",
	"char/0xf029/mathmode": "\\mathfrak{p}",
	"char/0xf02a/mathmode": "\\mathfrak{q}",
	"char/0xf02b/mathmode": "\\mathfrak{r}",
	"char/0xf02c/mathmode": "\\mathfrak{s}",
	"char/0xf02d/mathmode": "\\mathfrak{t}",
	"char/0xf02e/mathmode": "\\mathfrak{u}",
	"char/0xf02f/mathmode": "\\mathfrak{v}",
	"char/0xf030/mathmode": "\\mathfrak{w}",
	"char/0xf031/mathmode": "\\mathfrak{x}",
	"char/0xf032/mathmode": "\\mathfrak{y}",
	"char/0xf033/mathmode": "\\mathfrak{z}",
	"char/0xf080/mathmode": "\\mathbb{A}",
	"char/0xf081/mathmode": "\\mathbb{B}",
	"char/0xf082/mathmode": "\\mathbb{C}",
	"char/0xf083/mathmode": "\\mathbb{D}",
	"char/0xf084/mathmode": "\\mathbb{E}",
	"char/0xf085/mathmode": "\\mathbb{F}",
	"char/0xf086/mathmode": "\\mathbb{G}",
	"char/0xf087/mathmode": "\\mathbb{H}",
	"char/0xf088/mathmode": "\\mathbb{I}",
	"char/0xf089/mathmode": "\\mathbb{J}",
	"char/0xf08a/mathmode": "\\mathbb{K}",
	"char/0xf08b/mathmode": "\\mathbb{L}",
	"char/0xf08c/mathmode": "\\mathbb{M}",
	"char/0xf08d/mathmode": "\\mathbb{N}",
	"char/0xf08e/mathmode": "\\mathbb{O}",
	"char/0xf08f/mathmode": "\\mathbb{P}",
	"char/0xf090/mathmode": "\\mathbb{Q}",
	"char/0xf091/mathmode": "\\mathbb{R}",
	"char/0xf092/mathmode": "\\mathbb{S}",
	"char/0xf093/mathmode": "\\mathbb{T}",
	"char/0xf094/mathmode": "\\mathbb{U}",
	"char/0xf095/mathmode": "\\mathbb{V}",
	"char/0xf096/mathmode": "\\mathbb{W}",
	"char/0xf097/mathmode": "\\mathbb{X}",
	"char/0xf098/mathmode": "\\mathbb{Y}",
	"char/0xf099/mathmode": "\\mathbb{Z}",
	"char/0xf0a4/mathmode": "\\Bbbk ",
	"char/0xf100/mathmode": "\\mathcal{A}",
	"char/0xf101/mathmode": "\\mathcal{B}",
	"char/0xf102/mathmode": "\\mathcal{C}",
	"char/0xf103/mathmode": "\\mathcal{D}",
	"char/0xf104/mathmode": "\\mathcal{E}",
	"char/0xf105/mathmode": "\\mathcal{F}",
	"char/0xf106/mathmode": "\\mathcal{G}",
	"char/0xf107/mathmode": "\\mathcal{H}",
	"char/0xf108/mathmode": "\\mathcal{I}",
	"char/0xf109/mathmode": "\\mathcal{J}",
	"char/0xf10a/mathmode": "\\mathcal{K}",
	"char/0xf10b/mathmode": "\\mathcal{L}",
	"char/0xf10c/mathmode": "\\mathcal{M}",
	"char/0xf10d/mathmode": "\\mathcal{N}",
	"char/0xf10e/mathmode": "\\mathcal{O}",
	"char/0xf10f/mathmode": "\\mathcal{P}",
	"char/0xf110/mathmode": "\\mathcal{Q}",
	"char/0xf111/mathmode": "\\mathcal{R}",
	"char/0xf112/mathmode": "\\mathcal{S}",
	"char/0xf113/mathmode": "\\mathcal{T}",
	"char/0xf114/mathmode": "\\mathcal{U}",
	"char/0xf115/mathmode": "\\mathcal{V}",
	"char/0xf116/mathmode": "\\mathcal{W}",
	"char/0xf117/mathmode": "\\mathcal{X}",
	"char/0xf118/mathmode": "\\mathcal{Y}",
	"char/0xf119/mathmode": "\\mathcal{Z}",
	"char/0xec07":          "|",
	"char/0xec08":          "|",
	"char/0x003c":          " < ",
	"char/0x0302":          "\\widehat ",
	"char/0x86e0":          "\\prime ",
}


const oleCbHdr = uint16(28)

// [MTEFv5](https://docs.wiris.com/en/mathtype/mathtype_desktop/mathtype-sdk/mtef5)
type MTEFv5 struct {
	mMtefVer     uint8
	mPlatform    uint8
	mProduct     uint8
	mVersion     uint8
	mVersionSub  uint8
	mApplication string
	mInline      uint8

	reader io.ReadSeeker

	ast   *MtAST
	nodes []*MtAST

	//是否合法，顺利解析
	Valid bool
}

func (m *MTEFv5) readRecord() (err error) {
	/**
	读取body的每一行数据并保存到数组里
	*/
	//默认设置为合法的，除非遇到不可解析数据
	m.Valid = true

	//Header
	_ = binary.Read(m.reader, binary.LittleEndian, &m.mMtefVer)
	_ = binary.Read(m.reader, binary.LittleEndian, &m.mPlatform)
	_ = binary.Read(m.reader, binary.LittleEndian, &m.mProduct)
	_ = binary.Read(m.reader, binary.LittleEndian, &m.mVersion)
	_ = binary.Read(m.reader, binary.LittleEndian, &m.mVersionSub)
	m.mApplication, _ = m.readNullTerminatedString()
	_ = binary.Read(m.reader, binary.LittleEndian, &m.mInline)

	//fmt.Println(m.mMtefVer, m.mPlatform, m.mProduct, m.mVersion, m.mVersionSub)
	//fmt.Println(m.mInline)
	//fmt.Println(m.reader)

	//Body
	for {
		record := RecordType(0)
		err = binary.Read(m.reader, binary.LittleEndian, &record)

		// 根据future定义，>=100的后面会跟一个字节，这个字节代表需要跳过的长度
		//For now, readers can assume that an unsigned integer follows the record type and is the number of bytes following it in the record
		//This makes it easy for software that reads MTEF to skip these records.
		if record >= FUTURE {
			var skipFutureLength uint8
			_ = binary.Read(m.reader, binary.LittleEndian, &skipFutureLength)

			_, _ = m.reader.Seek(int64(skipFutureLength), io.SeekCurrent)
			continue
		}

		//debug 使用
		//fmt.Println(record)

		if err != nil {
			break
		}
		switch record {
		case END:
			m.nodes = append(m.nodes, &MtAST{END, nil, nil})
		case LINE:
			line := new(MtLine)
			_ = m.readLine(line)

			m.nodes = append(m.nodes, &MtAST{LINE, line, nil})
		case CHAR:
			char := new(MtChar)
			_ = m.readChar(char)

			m.nodes = append(m.nodes, &MtAST{CHAR, char, nil})
		case TMPL:
			tmpl := new(MtTmpl)
			_ = m.readTMPL(tmpl)

			m.nodes = append(m.nodes, &MtAST{TMPL, tmpl, nil})
		case PILE:
			pile := new(MtPile)
			_ = m.readPile(pile)

			m.nodes = append(m.nodes, &MtAST{PILE, pile, nil})
		case MATRIX:
			matrix := new(MtMatrix)
			_ = m.readMatrix(matrix)

			m.nodes = append(m.nodes, &MtAST{MATRIX, matrix, nil})

			//匹配矩阵数据下面的2个nil
			m.nodes = append(m.nodes, &MtAST{LINE, new(MtLine), nil})
			m.nodes = append(m.nodes, &MtAST{LINE, new(MtLine), nil})
		case EMBELL:
			embell := new(MtEmbellRd)
			_ = m.readEmbell(embell)

			m.nodes = append(m.nodes, &MtAST{tag: EMBELL, value: embell, children: nil})
		case FONT_STYLE_DEF:
			fsDef := new(MtfontStyleDef)
			_ = binary.Read(m.reader, binary.LittleEndian, &fsDef.fontDefIndex)
			fsDef.name, _ = m.readNullTerminatedString()

			//读取字节，但是不关心数据，注释
			//m.nodes = append(m.nodes, &MtAST{FONT_STYLE_DEF, fsDef, nil})
		case SIZE:
			mtSize := new(MtSize)
			_ = binary.Read(m.reader, binary.LittleEndian, &mtSize.lsize)
			_ = binary.Read(m.reader, binary.LittleEndian, &mtSize.dsize)
		case SUB:
			m.nodes = append(m.nodes, &MtAST{SUB, nil, nil})
		case SUB2:
			m.nodes = append(m.nodes, &MtAST{SUB2, nil, nil})
		case SYM:
			m.nodes = append(m.nodes, &MtAST{SYM, nil, nil})
		case SUBSYM:
			m.nodes = append(m.nodes, &MtAST{SUBSYM, nil, nil})
		case FONT_DEF:
			fdef := new(MtfontDef)
			_ = binary.Read(m.reader, binary.LittleEndian, &fdef.encDefIndex)
			fdef.name, _ = m.readNullTerminatedString()

			m.nodes = append(m.nodes, &MtAST{FONT_DEF, fdef, nil})
		case COLOR:
			cIndex := new(MtColorDefIndex)
			_ = binary.Read(m.reader, binary.LittleEndian, &cIndex.index)

			//读取字节，但是不关心数据，注释
			//m.nodes = append(m.nodes, &MtAST{tag: COLOR, value: cIndex, children: nil})
		case COLOR_DEF:
			cDef := new(MtColorDef)
			_ = m.readColorDef(cDef)

			//读取字节，但是不关心数据，注释
			//m.nodes = append(m.nodes, &MtAST{tag: COLOR_DEF, value: cDef, children: nil})
		case FULL:
			m.nodes = append(m.nodes, &MtAST{FULL, nil, nil})
		case EQN_PREFS:
			prefs := new(MtEqnPrefs)
			_ = m.readEqnPrefs(prefs)

			m.nodes = append(m.nodes, &MtAST{EQN_PREFS, prefs, nil})
		case ENCODING_DEF:
			enc, _ := m.readNullTerminatedString()

			m.nodes = append(m.nodes, &MtAST{ENCODING_DEF, enc, nil})
		default:
			m.Valid = false
			log.Println("FUTURE RECORD", record)
		}
	}

	return nil
}

func (m *MTEFv5) readNullTerminatedString() (s string, err error) {
	buf, p := bytes.Buffer{}, []byte{0}
	for {
		_, err = m.reader.Read(p)
		if p[0] == 0 {
			break
		}
		buf.WriteByte(p[0])
	}
	return buf.String(), err
}

func (m *MTEFv5) readLine(line *MtLine) (err error) {
	options := OptionType(0)
	err = binary.Read(m.reader, binary.LittleEndian, &options)

	if MtefOptNudge == MtefOptNudge&options {
		line.nudgeX, line.nudgeY, _ = m.readNudge()
	}
	if MtefOptLineLspace == MtefOptLineLspace&options {
		_ = binary.Read(m.reader, binary.LittleEndian, &line.lineSpace)
	}

	//RULER解析
	if mtefOPT_LP_RULER == mtefOPT_LP_RULER&options {
		var nStops uint8
		_ = binary.Read(m.reader, binary.LittleEndian, &nStops)

		var tabList []uint8
		for i := uint8(0); i < nStops; i++ {
			var stopVal uint8
			_ = binary.Read(m.reader, binary.LittleEndian, &stopVal)
			tabList = append(tabList, stopVal)

			var tabOffset uint16
			_ = binary.Read(m.reader, binary.LittleEndian, &tabOffset)
		}
	}

	if MtefOptLineNull == MtefOptLineNull&options {
		line.null = true
	}

	return err
}

func (m *MTEFv5) readDimensionArrays(size int64) (array []string, err error) {
	var flag = true
	var tmpStr = new(bytes.Buffer)
	var count = int64(0)

	var fx = func(x uint8) {
		if flag {
			switch x {
			case 0x00:
				flag = false
				tmpStr.WriteString("in")
			case 0x01:
				flag = false
				tmpStr.WriteString("cm")
			case 0x02:
				flag = false
				tmpStr.WriteString("pt")
			case 0x03:
				flag = false
				tmpStr.WriteString("pc")
			case 0x04:
				flag = false
				tmpStr.WriteString("%")
			default:
				fmt.Println("invalid bytes")
			}
		} else {
			switch x {
			case 0x00:
				flag = false
				tmpStr.WriteByte('0')
			case 0x01:
				flag = false
				tmpStr.WriteByte('1')
			case 0x02:
				flag = false
				tmpStr.WriteByte('2')
			case 0x03:
				flag = false
				tmpStr.WriteByte('3')
			case 0x04:
				flag = false
				tmpStr.WriteByte('4')
			case 0x05:
				flag = false
				tmpStr.WriteByte('5')
			case 0x06:
				flag = false
				tmpStr.WriteByte('6')
			case 0x07:
				flag = false
				tmpStr.WriteByte('7')
			case 0x08:
				flag = false
				tmpStr.WriteByte('8')
			case 0x09:
				flag = false
				tmpStr.WriteByte('9')
			case 0x0a:
				flag = false
				tmpStr.WriteByte('.')
			case 0x0b:
				flag = false
				tmpStr.WriteByte('-')
			case 0x0f:
				flag = true
				count += 1
				array = append(array, tmpStr.String())
				tmpStr.Reset()
			default:
				fmt.Println("invalid bytes")
			}
		}
	}

	for {
		if count >= size {
			//fmt.Println("break with size=", size)
			break
		}
		ch := uint8(0)
		_ = binary.Read(m.reader, binary.LittleEndian, &ch)

		//fmt.Println("ch=", ch)

		hi := (ch & 0xf0) / 16
		lo := ch & 0x0f
		fx(hi)
		fx(lo)
	}
	return array, nil
}

func (m *MTEFv5) readEqnPrefs(eqnPrefs *MtEqnPrefs) (err error) {
	options := uint8(0)
	_ = binary.Read(m.reader, binary.LittleEndian, &options)

	//sizes
	size := uint8(0)
	_ = binary.Read(m.reader, binary.LittleEndian, &size)
	eqnPrefs.sizes, _ = m.readDimensionArrays(int64(size))

	//spaces
	size = 0
	_ = binary.Read(m.reader, binary.LittleEndian, &size)
	eqnPrefs.spaces, _ = m.readDimensionArrays(int64(size))

	//styles
	size = 0
	_ = binary.Read(m.reader, binary.LittleEndian, &size)
	styles := make([]byte, size)
	for i := uint8(0); i < size; i++ {
		c := uint8(0)
		_ = binary.Read(m.reader, binary.LittleEndian, &c)
		if c == 0 {
			styles = append(styles, 0)
		} else {
			_ = binary.Read(m.reader, binary.LittleEndian, &c)
			styles = append(styles, c)
		}
	}
	eqnPrefs.styles = styles
	return nil
}

func (m *MTEFv5) readChar(char *MtChar) (err error) {
	options := OptionType(0)
	_ = binary.Read(m.reader, binary.LittleEndian, &options)

	if MtefOptNudge == MtefOptNudge&options {
		char.nudgeX, char.nudgeY, _ = m.readNudge()
	}

	_ = binary.Read(m.reader, binary.LittleEndian, &char.typeface)

	if MtefOptCharEncNoMtcode != MtefOptCharEncNoMtcode&options {
		_ = binary.Read(m.reader, binary.LittleEndian, &char.mtcode)
	}
	if MtefOptCharEncChar8 == MtefOptCharEncChar8&options {
		//todo 强行设置值，有BUG。。。。。
		//if char.mtcode >= 34528 {
		//	_ = binary.Read(m.reader, binary.LittleEndian, &char.bits16)
		//}else {
		_ = binary.Read(m.reader, binary.LittleEndian, &char.bits8)
		//}
	}
	if MtefOptCharEncChar16 == MtefOptCharEncChar16&options {
		_ = binary.Read(m.reader, binary.LittleEndian, &char.bits16)
	}

	//fmt.Println(char)

	return nil
}

func (m *MTEFv5) readNudge() (nudgeX int16, nudgeY int16, err error) {
	b1 := 0
	b2 := 0
	_ = binary.Read(m.reader, binary.LittleEndian, &b1)
	_ = binary.Read(m.reader, binary.LittleEndian, &b2)

	if b1 == 128 || b2 == 128 {
		_ = binary.Read(m.reader, binary.LittleEndian, &nudgeX)
		_ = binary.Read(m.reader, binary.LittleEndian, &nudgeY)
		return nudgeX, nudgeY, err
	} else {
		nudgeX = int16(b1)
		nudgeY = int16(b2)
		return nudgeX, nudgeY, err
	}
}

func (m *MTEFv5) readTMPL(tmpl *MtTmpl) (err error) {
	options := OptionType(0)
	_ = binary.Read(m.reader, binary.LittleEndian, &options)

	if MtefOptNudge == MtefOptNudge&options {
		tmpl.nudgeX, tmpl.nudgeY, _ = m.readNudge()
	}
	_ = binary.Read(m.reader, binary.LittleEndian, &tmpl.selector)

	// variation, 1 or 2 bytes
	byte1 := uint8(0)
	_ = binary.Read(m.reader, binary.LittleEndian, &byte1)
	if 0x80 == byte1&0x80 {
		byte2 := uint8(0)
		_ = binary.Read(m.reader, binary.LittleEndian, &byte2)
		tmpl.variation = (uint16(byte1) & 0x7F) | (uint16(byte2) << 8)
	} else {
		tmpl.variation = uint16(byte1)
	}
	_ = binary.Read(m.reader, binary.LittleEndian, &tmpl.options)
	return nil
}

func (m *MTEFv5) readPile(pile *MtPile) (err error) {
	options := OptionType(0)
	_ = binary.Read(m.reader, binary.LittleEndian, &options)

	if MtefOptNudge == MtefOptNudge&options {
		pile.nudgeX, pile.nudgeY, _ = m.readNudge()
	}

	//读取halign和valign
	_ = binary.Read(m.reader, binary.LittleEndian, &pile.halign)
	_ = binary.Read(m.reader, binary.LittleEndian, &pile.valign)

	return nil
}

func (m *MTEFv5) readMatrix(matrix *MtMatrix) (err error) {
	options := OptionType(0)
	_ = binary.Read(m.reader, binary.LittleEndian, &options)

	if MtefOptNudge == MtefOptNudge&options {
		matrix.nudgeX, matrix.nudgeY, _ = m.readNudge()
	}

	//读取valign和h_just、v_just
	_ = binary.Read(m.reader, binary.LittleEndian, &matrix.valign)
	_ = binary.Read(m.reader, binary.LittleEndian, &matrix.h_just)
	_ = binary.Read(m.reader, binary.LittleEndian, &matrix.v_just)

	//读取rows和cols
	_ = binary.Read(m.reader, binary.LittleEndian, &matrix.rows)
	_ = binary.Read(m.reader, binary.LittleEndian, &matrix.cols)

	//fmt.Printf("%v", matrix)
	return nil
}

func (m *MTEFv5) readEmbell(embell *MtEmbellRd) (err error) {
	options := OptionType(0)
	_ = binary.Read(m.reader, binary.LittleEndian, &options)

	if MtefOptNudge == MtefOptNudge&options {
		embell.nudgeX, embell.nudgeY, _ = m.readNudge()
	}

	//读取embellishment type
	_ = binary.Read(m.reader, binary.LittleEndian, &embell.embellType)
	return nil
}

func (m *MTEFv5) readColorDef(colorDef *MtColorDef) (err error) {
	options := OptionType(0)
	_ = binary.Read(m.reader, binary.LittleEndian, &options)

	var color uint16
	if mtefCOLOR_CMYK == mtefCOLOR_CMYK&options {
		//CMYK，读4个值
		for i := 0; i < 4; i++ {
			_ = binary.Read(m.reader, binary.LittleEndian, &color)
			colorDef.values = append(colorDef.values, uint8(color))
		}
	} else {
		//	RGB，读3个值
		for i := 0; i < 3; i++ {
			_ = binary.Read(m.reader, binary.LittleEndian, &color)
			colorDef.values = append(colorDef.values, uint8(color))
		}
	}

	if mtefCOLOR_NAME == mtefCOLOR_NAME&options {
		colorDef.name, _ = m.readNullTerminatedString()
	}

	return nil
}

func (m *MTEFv5) Translate() string {
	latexStr, err := m.makeLatex(m.ast)
	if err != nil {
		fmt.Println(err)
	}

	if m.Valid {
		return latexStr
	} else {
		return ""
	}

}

func (m *MTEFv5) makeAST() (err error) {
	/**
	根据数组生成出栈入栈结构
	*/
	ast := new(MtAST)
	ast.tag = 0xff
	ast.value = nil
	m.ast = ast

	stack := list.New()
	stack.PushBack(ast)

	for _, node := range m.nodes {
		//debug 可用
		//fmt.Printf("%+v %+v \n", node.tag, node.value)

		switch node.tag {
		case LINE:
			if stack.Len() > 0 {
				ele := stack.Back()

				//将对象强制转为MtAST类型
				parent := ele.Value.(*MtAST)

				parent.children = append(parent.children, node)
			}
			if !node.value.(*MtLine).null {
				//如果与0 <nil> 匹配，则需要入栈
				stack.PushBack(node)
			}
		case TMPL:
			if stack.Len() > 0 {
				ele := stack.Back()

				//将对象强制转为MtAST类型
				parent := ele.Value.(*MtAST)

				parent.children = append(parent.children, node)
			}

			//如果与0 <nil> 匹配，则需要入栈
			stack.PushBack(node)
		case PILE:
			if stack.Len() > 0 {
				ele := stack.Back()

				//将对象强制转为MtAST类型
				parent := ele.Value.(*MtAST)

				parent.children = append(parent.children, node)
			}

			//如果与0 <nil> 匹配，则需要入栈
			stack.PushBack(node)
		case MATRIX:
			if stack.Len() > 0 {
				ele := stack.Back()

				//将对象强制转为MtAST类型
				parent := ele.Value.(*MtAST)

				parent.children = append(parent.children, node)
			}

			//如果与0 <nil> 匹配，则需要入栈
			stack.PushBack(node)
		case END:
			if stack.Len() > 0 {
				ele := stack.Back()
				stack.Remove(ele)
			}
		case CHAR:
			if stack.Len() > 0 {
				ele := stack.Back()

				//将对象强制转为MtAST类型
				parent := ele.Value.(*MtAST)

				parent.children = append(parent.children, node)
			} else if stack.Len() == 0 {
				//never go there
				ast.children = append(ast.children, node)
			}
		case EMBELL:
			if stack.Len() > 0 {
				//读取父节点
				ele := stack.Back()

				//并将对象强制转为MtAST类型
				parent := ele.Value.(*MtAST)
				parent.children = append(parent.children, node)

				switch EmbellType(node.value.(*MtEmbellRd).embellType) {
				//数据结构中，这些数据是在字符后面，但是在latex展示中某些字符需要在字符前面
				//比如： $$ \hat y $$
				//所以我们需要交换最后2位
				case emb1DOT, embHAT, embOBAR:
					if len(parent.children) >= 2 {
						embellData := parent.children[len(parent.children)-1]
						charData := parent.children[len(parent.children)-2]
						parent.children = parent.children[:len(parent.children)-2]

						parent.children = append(parent.children, embellData, charData)
					}
				}
			}

			//如果与0 <nil> 匹配，则需要入栈
			stack.PushBack(node)

			//case COLOR_DEF:
			//	/*
			//	这个数据结构有3或4个（RGB或者CMYK）对应的nil，所以需要循环把每个值都push到栈里面
			//
			//	16 &{values:[0 0 0] name:}
			//	0 <nil>
			//	0 <nil>
			//	0 <nil>
			//	 */
			//
			//	colorList := node.value.(*MtColorDef).values
			//	if len(colorList) > 0 {
			//		//读取每个color的值，然后入栈
			//		for _, val := range colorList {
			//			//如果与0 <nil> 匹配，则需要入栈
			//			stack.PushBack(val)
			//		}
			//	}
			//case FONT_STYLE_DEF:
			//	/*
			//	这个数据结构如下，所以需要配对6个入栈
			//	8 &{fontDefIndex:1 name:}
			//	0 <nil>
			//	0 <nil>
			//	0 <nil>
			//	0 <nil>
			//	0 <nil>
			//	0 <nil>
			//	*/
			//
			//	fontIndex := node.value.(*MtfontStyleDef).fontDefIndex
			//	if fontIndex == 1 {
			//		for i := 0; i < 6; i++ {
			//			//如果与0 <nil> 匹配，则需要入栈
			//			stack.PushBack(0)
			//		}
			//	}
		}
	}

	//m.ast.debug(0)
	return nil
}

func (m *MTEFv5) makeLatex(ast *MtAST) (latex string, err error) {
	/**
	根据出栈入栈结构生成latex字符串
	*/

	buf := new(bytes.Buffer)

	switch ast.tag {
	case ROOT:
		buf.WriteString("$$ ")
		for _, _ast := range ast.children {
			_latex, _ := m.makeLatex(_ast)
			buf.WriteString(_latex)
		}
		buf.WriteString(" $$")
		return buf.String(), nil
	case CHAR:
		mtcode := ast.value.(*MtChar).mtcode
		typeface := ast.value.(*MtChar).typeface
		char := string(mtcode)

		//生成char的一些特殊集
		hexExtend := ""
		typefaceFmt := ""
		switch typeface - 128 {
		case fnMTEXTRA:
			hexExtend = "/mathmode"
		case fnSPACE:
			hexExtend = "/mathmode"
		case fnTEXT:
			typefaceFmt = "{ \\rm{ %v } }"
		}

		//生成扩展字符的key
		hexCode := fmt.Sprintf("%04x", mtcode)
		hexKey := fmt.Sprintf("char/0x%v%v", hexCode, hexExtend)

		//fmt.Println(char, hexKey)

		//首先去找扩展字符
		sChar, ok := Chars[hexKey]
		if ok {
			char = sChar
		} else {
			//如果char是特殊symbol，需要转义
			sChar, ok := SpecialChar[char]
			if ok {
				char = sChar
			}
		}

		//确定字符是否为文本，如果是文本，则需要包一层
		if typefaceFmt != "" {
			char = fmt.Sprintf(typefaceFmt, char)
		}

		buf.WriteString(char)
		return buf.String(), nil
	case TMPL:
		//强制类型转换为MtTmpl
		tmpl := ast.value.(*MtTmpl)

		switch SelectorType(tmpl.selector) {
		case tmANGLE:
			mainAST := ast.children[0]
			leftAST := ast.children[1]
			rightAST := ast.children[2]

			mainSlot, _ := m.makeLatex(mainAST)
			leftSlot, _ := m.makeLatex(leftAST)
			rightSlot, _ := m.makeLatex(rightAST)

			//转成latex代码
			var mainStr, leftStr, rightStr string
			if mainSlot != "" {
				mainStr = fmt.Sprintf("{ %v }", mainSlot)
			}
			if leftSlot != "" {
				leftStr = fmt.Sprintf("\\left %v", leftSlot)
			}
			if rightSlot != "" {
				rightStr = fmt.Sprintf("\\right %v", rightSlot)
			}

			buf.WriteString(fmt.Sprintf("%v %v %v", leftStr, mainStr, rightStr))
			return buf.String(), nil

		case tmPAREN:
			mainAST := ast.children[0]
			leftAST := ast.children[1]
			rightAST := ast.children[2]

			mainSlot, _ := m.makeLatex(mainAST)
			leftSlot, _ := m.makeLatex(leftAST)
			rightSlot, _ := m.makeLatex(rightAST)

			//转成latex代码
			var mainStr, leftStr, rightStr string
			if mainSlot != "" {
				mainStr = fmt.Sprintf("{ %v }", mainSlot)
			}
			if leftSlot != "" {
				leftStr = fmt.Sprintf("\\left %v", leftSlot)
			}
			if rightSlot != "" {
				rightStr = fmt.Sprintf("\\right %v", rightSlot)
			}

			buf.WriteString(fmt.Sprintf("%v %v %v", leftStr, mainStr, rightStr))
			return buf.String(), nil
		case tmBRACE:
			var mainSlot, leftSlot, rightSlot string
			for idx, astData := range ast.children {
				if idx == 0 {
					mainSlot, _ = m.makeLatex(astData)
				} else if idx == 1 {
					leftSlot, _ = m.makeLatex(astData)
				} else {
					rightSlot, _ = m.makeLatex(astData)
				}
			}

			if rightSlot == "" {
				rightSlot = "."
			} else {
				rightSlot = " " + rightSlot
			}

			//组装公式
			buf.WriteString(fmt.Sprintf(
				"\\left %v \\begin{array}{l} %v \\end{array} \\right%v",
				leftSlot, mainSlot, rightSlot))

			return buf.String(), nil
		case tmBRACK:
			mainAST := ast.children[0]
			leftAST := ast.children[1]
			rightAST := ast.children[2]
			mainSlot, _ := m.makeLatex(mainAST)
			if mainSlot == "" {
				mainSlot = "\\space"
			}
			leftSlot, _ := m.makeLatex(leftAST)
			rightSlot, _ := m.makeLatex(rightAST)
			buf.WriteString(fmt.Sprintf("\\left%v %v \\right%v", leftSlot, mainSlot, rightSlot))
			return buf.String(), nil
		case tmBAR:
			//读取数据 ParBoxClass
			var mainSlot, leftSlot, rightSlot string
			for idx, astData := range ast.children {
				if idx == 0 {
					mainSlot, _ = m.makeLatex(astData)
				} else if idx == 1 {
					leftSlot, _ = m.makeLatex(astData)
				} else {
					rightSlot, _ = m.makeLatex(astData)
				}
			}

			if rightSlot == "" {
				rightSlot = "."
			} else {
				rightSlot = " " + rightSlot
			}

			//转成latex代码
			var mainStr, leftStr, rightStr string
			if mainSlot != "" {
				mainStr = fmt.Sprintf("{ %v }", mainSlot)
			}
			if leftSlot != "" {
				leftStr = fmt.Sprintf("\\left %v", leftSlot)
			}
			if rightSlot != "" {
				rightStr = fmt.Sprintf("\\right %v", rightSlot)
			}

			//组成整体公式
			tmplStr := fmt.Sprintf("%v %v %v", leftStr, mainStr, rightStr)
			buf.WriteString(tmplStr)

			return buf.String(), nil
		case tmINTERVAL:
			//读取数据 ParBoxClass
			mainAST := ast.children[0]
			leftAST := ast.children[1]
			rightAST := ast.children[2]

			//读取latex数据
			mainSlot, _ := m.makeLatex(mainAST)
			leftSlot, _ := m.makeLatex(leftAST)
			rightSlot, _ := m.makeLatex(rightAST)

			//转成latex代码
			var mainStr, leftStr, rightStr string
			if mainSlot != "" {
				mainStr = fmt.Sprintf("{ %v }", mainSlot)
			}
			if leftSlot != "" {
				leftStr = fmt.Sprintf("\\left %v", leftSlot)
			}
			if rightSlot != "" {
				rightStr = fmt.Sprintf("\\right %v", rightSlot)
			}

			//组成整体公式
			tmplStr := fmt.Sprintf("%v %v %v", leftStr, mainStr, rightStr)
			buf.WriteString(tmplStr)

			return buf.String(), nil
		case tmROOT:
			mainAST := ast.children[0]
			radiAST := ast.children[1]
			mainSlot, _ := m.makeLatex(mainAST)
			radiSlot, _ := m.makeLatex(radiAST)
			buf.WriteString(fmt.Sprintf("\\sqrt[%v] { %v }", radiSlot, mainSlot))
			return buf.String(), nil
		case tmFRACT:
			numAST := ast.children[0]
			denAST := ast.children[1]
			numSlot, _ := m.makeLatex(numAST)
			denSlot, _ := m.makeLatex(denAST)
			buf.WriteString(fmt.Sprintf("\\frac { %v } { %v }", numSlot, denSlot))
			return buf.String(), nil
		case tmARROW:
			/*
				variation	symbol	description
				0×0000	tvAR_SINGLE	single arrow
				0×0001	tvAR_DOUBLE	double arrow
				0×0002	tvAR_HARPOON	harpoon
				0×0004	tvAR_TOP	top slot is present
				0×0008	tvAR_BOTTOM	bottom slot is present
				0×0010	tvAR_LEFT	if single, arrow points left
				0×0020	tvAR_RIGHT	if single, arrow points right
				0×0010	tvAR_LOS	if double or harpoon, large over small
				0×0020	tvAR_SOL	if double or harpoon, small over large
			*/
			topAST := ast.children[0]
			bottomAST := ast.children[1]

			//读取latex数据
			topSlot, _ := m.makeLatex(topAST)
			bottomSlot, _ := m.makeLatex(bottomAST)

			//转成latex代码
			var topStr, bottomStr string
			if topSlot != "" {
				topStr = fmt.Sprintf("{\\mathrm{ %v }}", topSlot)
			}
			if bottomSlot != "" {
				bottomStr = fmt.Sprintf("[\\mathrm{ %v }]", bottomSlot)
			}

			/*
				variation转码
			*/
			variationsMap := make(map[uint16]string)
			variationsMap[0x0000] = "single"
			variationsMap[0x0001] = "double"
			variationsMap[0x0002] = "harpoon"
			variationsMap[0x0004] = "topSlotPresent"
			variationsMap[0x0008] = "bottomSlotPresent"
			variationsMap[0x0010] = "pointLeft"
			variationsMap[0x0020] = "pointRight"

			//有序循环
			variationsCode := []uint16{0x0000, 0x0001, 0x0002, 0x0004, 0x0008, 0x0010, 0x0020}

			arrowStyle := "single"
			latexFmt := "\\x"
			for _, vCode := range variationsCode {
				//如果存在掩码
				if vCode&uint16(tmpl.variation) != 0 {
					//判断类型，默认是single
					if variationsMap[vCode] == "double" {
						arrowStyle = "double"
					} else if variationsMap[vCode] == "harpoon" {
						arrowStyle = "harpoon"
					}

					if arrowStyle == "single" && variationsMap[vCode] == "pointLeft" {
						latexFmt = latexFmt + "leftarrow"
					} else if arrowStyle == "double" && variationsMap[vCode] == "pointLeft" {
						fmt.Println("not implement double , large over small")
					} else if arrowStyle == "harpoon" && variationsMap[vCode] == "pointLeft" {
						fmt.Println("not implement harpoon, large over small")
					}

					if arrowStyle == "single" && variationsMap[vCode] == "pointRight" {
						latexFmt = latexFmt + "rightarrow"
					} else if arrowStyle == "double" && variationsMap[vCode] == "pointRight" {
						fmt.Println("not implement double , small over large")
					} else if arrowStyle == "harpoon" && variationsMap[vCode] == "pointRight" {
						fmt.Println("not implement harpoon, small over large")
					}
				}
			}
			/*
				variation转码 END
			*/

			//组成整体公式
			tmplStr := fmt.Sprintf("%v %v %v", latexFmt, bottomStr, topStr)
			buf.WriteString(tmplStr)

			return buf.String(), nil
		case tmUBAR:
			//读取数据
			mainAST := ast.children[0]

			//读取latex数据
			mainSlot, _ := m.makeLatex(mainAST)

			//转成latex代码
			var mainStr string
			if mainSlot != "" {
				mainStr = fmt.Sprintf(" {\\underline{ %v }} ", mainSlot)
			}

			//组成整体公式
			tmplStr := fmt.Sprintf(" %v ", mainStr)
			buf.WriteString(tmplStr)

			//返回数据
			return buf.String(), nil
		case tmSUM:
			//读取数据 BigOpBoxClass
			var mainSlot, upperSlot, lowerSlot, operatorSlot string
			for idx, astData := range ast.children {
				if idx == 0 {
					mainSlot, _ = m.makeLatex(astData)
				} else if idx == 1 {
					lowerSlot, _ = m.makeLatex(astData)
				} else if idx == 2 {
					upperSlot, _ = m.makeLatex(astData)
				} else {
					operatorSlot, _ = m.makeLatex(astData)
				}
			}

			//转成latex代码
			var mainStr, lowerStr, upperStr string
			if mainSlot != "" {
				mainStr = fmt.Sprintf("{ %v }", mainSlot)
			}
			if lowerSlot != "" {
				lowerStr = fmt.Sprintf("\\limits_{ %v }", lowerSlot)
			}
			if upperSlot != "" {
				upperStr = fmt.Sprintf("^ %v", upperSlot)
			}

			//组成整体公式
			tmplStr := fmt.Sprintf("%v %v %v %v", operatorSlot, lowerStr, upperStr, mainStr)
			buf.WriteString(tmplStr)

			return buf.String(), nil
		case tmLIM:
			//读取数据 LimBoxClass
			var mainSlot, lowerSlot, upperSlot string
			for idx, astData := range ast.children {
				if idx == 0 {
					mainSlot, _ = m.makeLatex(astData)
				} else if idx == 1 {
					lowerSlot, _ = m.makeLatex(astData)
				} else {
					upperSlot, _ = m.makeLatex(astData)
				}
			}

			//转成latex代码
			var mainStr, lowerStr, upperStr string
			if mainSlot != "" {
				mainStr = fmt.Sprintf("\\mathop { %v }", mainSlot)
			}
			if lowerSlot != "" {
				lowerStr = fmt.Sprintf("\\limits_{ %v }", lowerSlot)
			}
			if upperSlot != "" {
				upperStr = ""
			}

			//组成整体公式
			tmplStr := fmt.Sprintf("%v %v %v", mainStr, lowerStr, upperStr)
			buf.WriteString(tmplStr)

			return buf.String(), nil
		case tmSUP:
			subAST := ast.children[0]
			supAST := ast.children[1]
			subSlot, _ := m.makeLatex(subAST)
			supSlot, _ := m.makeLatex(supAST)

			buf.WriteString(" ^ { ")
			buf.WriteString(supSlot)
			buf.WriteString(" } ")
			if subSlot != "" {
				buf.WriteString(" { ")
				buf.WriteString(subSlot)
				buf.WriteString(" } ")
			}
			return buf.String(), nil
		case tmSUB:
			//读取下标和上标
			subAST := ast.children[0]
			supAST := ast.children[1]

			//读取latex数据
			subSlot, _ := m.makeLatex(subAST)
			supSlot, _ := m.makeLatex(supAST)

			//转成latex代码
			var subFmt, supFmt string
			if subSlot != "" {
				subFmt = fmt.Sprintf("_{ %v }", subSlot)
			}
			if supSlot != "" {
				supFmt = fmt.Sprintf("^{ %v }", supSlot)
			}

			//组成整体公式
			tmplStr := fmt.Sprintf("%v  %v", subFmt, supFmt)
			buf.WriteString(tmplStr)

			//返回数据
			return buf.String(), nil
		case tmSUBSUP:
			//读取下标和上标
			subAST := ast.children[0]
			supAST := ast.children[1]

			//读取latex数据
			subSlot, _ := m.makeLatex(subAST)
			supSlot, _ := m.makeLatex(supAST)

			//转成latex代码
			var subFmt, supFmt string
			if subSlot != "" {
				subFmt = fmt.Sprintf("_{ %v }", subSlot)
			}
			if supSlot != "" {
				supFmt = fmt.Sprintf("^{ %v }", supSlot)
			}

			//组成整体公式
			tmplStr := fmt.Sprintf("%v  %v", subFmt, supFmt)
			buf.WriteString(tmplStr)

			//返回数据
			return buf.String(), nil
		case tmVEC:
			/*
				variations：
				variation	symbol	description
				0×0001	tvVE_LEFT	arrow points left
				0×0002	tvVE_RIGHT	arrow points right
				0×0004	tvVE_UNDER	arrow under slot, else over slot
				0×0008	tvVE_HARPOON	harpoon

				这个转换是通过掩码计算的：
				比如variation的值是3，即0000 0000 0000 0011

				对应的是0×0001和0×0002：
				0000 0000 0000 0001
				0000 0000 0000 0010
			*/

			//读取数据 HatBoxClass
			mainAST := ast.children[0]

			//读取latex数据
			mainSlot, _ := m.makeLatex(mainAST)

			//转成latex代码
			var mainStr string
			if mainSlot != "" {
				mainStr = fmt.Sprintf("{ %v }", mainSlot)
			}

			/*
				variation转码
			*/
			variationsMap := make(map[uint16]string)
			variationsMap[0x0001] = "left"
			variationsMap[0x0002] = "right"
			variationsMap[0x0004] = "tvVE_UNDER"
			variationsMap[0x0008] = "harpoonup"

			//有序循环
			variationsCode := []uint16{0x0001, 0x0002, 0x0004, 0x0008}

			topStr := "\\overset\\"
			for _, vCode := range variationsCode {
				if vCode&uint16(tmpl.variation) != 0 {
					topStr = topStr + variationsMap[vCode]
				}
			}

			//如果variationCode小于8，则一定不是harpoon,那么默认就使用arrow
			if tmpl.variation < 8 {
				topStr = topStr + "arrow"
			}
			/*
				variation转码 END
			*/

			//组成整体公式
			tmplStr := fmt.Sprintf("%v %v", topStr, mainStr)
			buf.WriteString(tmplStr)

			return buf.String(), nil
		case tmHAT:
			//读取数据 HatBoxClass
			mainAST := ast.children[0]
			topAST := ast.children[1]

			//读取latex数据
			mainSlot, _ := m.makeLatex(mainAST)
			topSlot, _ := m.makeLatex(topAST)

			//转成latex代码
			var mainStr, topStr string
			if mainSlot != "" {
				mainStr = fmt.Sprintf("{ %v }", mainSlot)
			}
			if topSlot != "" {
				topStr = fmt.Sprintf(" %v ", topSlot)
			}

			//组成整体公式
			tmplStr := fmt.Sprintf("%v %v", topStr, mainStr)
			buf.WriteString(tmplStr)

			return buf.String(), nil
		case tmARC:
			//读取数据 HatBoxClass
			mainAST := ast.children[0]
			topAST := ast.children[1]

			//读取latex数据
			mainSlot, _ := m.makeLatex(mainAST)
			topSlot, _ := m.makeLatex(topAST)

			//转成latex代码
			var mainStr, topStr string
			if mainSlot != "" {
				mainStr = fmt.Sprintf("{ %v }", mainSlot)
			}
			if topSlot != "" {
				topStr = fmt.Sprintf("\\overset %v", topSlot)
			}

			//组成整体公式
			tmplStr := fmt.Sprintf("%v %v", topStr, mainStr)
			buf.WriteString(tmplStr)

			return buf.String(), nil
		default:
			m.Valid = false
			log.Println("TMPL NOT IMPLEMENT", tmpl.selector, tmpl.variation)
		}
		for _, _ast := range ast.children {
			_latex, _ := m.makeLatex(_ast)
			buf.WriteString(_latex)
		}
		return buf.String(), nil
	case PILE:
		for idx, _ast := range ast.children {
			_latex, _ := m.makeLatex(_ast)

			//多个line字符串数据以 \\ 分割
			if idx > 0 {
				buf.WriteString(" \\\\ ")
			}

			buf.WriteString(_latex)
		}
		return buf.String(), nil
	case MATRIX:
		matrixCol := int(ast.value.(*MtMatrix).cols)
		for idx, _ast := range ast.children {
			_latex, _ := m.makeLatex(_ast)

			if idx == 0 {
				buf.WriteString(" \\begin{array} {} ")
				continue
			}

			buf.WriteString(_latex)

			if idx%matrixCol == 0 {
				buf.WriteString(" \\\\ ")
			} else {
				buf.WriteString(" & ")
			}
		}

		buf.WriteString(" \\end{array} ")
		return buf.String(), nil
	case LINE:
		for _, _ast := range ast.children {
			_latex, _ := m.makeLatex(_ast)
			buf.WriteString(_latex)
		}
		return buf.String(), nil
	case EMBELL:
		embellType := EmbellType(ast.value.(*MtEmbellRd).embellType)
		var embellStr string

		switch embellType {
		case emb1DOT:
			embellStr = " \\dot "
		case emb1PRIME:
			embellStr = "'"
		case emb2PRIME:
			embellStr = "''"
		case emb3PRIME:
			embellStr = "'''"
		case embHAT:
			embellStr = " \\hat "
		case embOBAR:
			embellStr = " \\bar "
		default:
			log.Println("not implement embell:", embellType)
		}

		buf.WriteString(embellStr)
		return buf.String(), nil
	}

	return "", nil
}

//[MTEF Storage](https://docs.wiris.com/en/mathtype/mathtype_desktop/mathtype-sdk/mtefstorage)
func Open(reader io.ReadSeeker) (eqn *MTEFv5, err error) {
	//parse `mtef` stream from `ole` object
	ole, err := ole2.Open(reader, "")
	if err != nil {
		fmt.Println(err)
	}

	dir, err := ole.ListDir()
	if err != nil {
		fmt.Println(err)
	}

	for _, file := range dir {
		if "Equation Native" == file.Name() {
			root := dir[0]
			reader := ole.OpenFile(file, root)

			hdrBuffer := make([]byte, oleCbHdr)
			if _, err := reader.Read(hdrBuffer); err == nil {
				hdrReader := bytes.NewReader(hdrBuffer)
				var cbHdr = uint16(0)
				var cbSize = uint32(0)

				_ = binary.Read(hdrReader, binary.LittleEndian, &cbHdr)
				if cbHdr != oleCbHdr {
					return nil, err
				}

				//ignore `version: u32` and `cf: u16`
				_, _ = hdrReader.Seek(4+2, io.SeekCurrent)
				_ = binary.Read(hdrReader, binary.LittleEndian, &cbSize)

				//body from `cbHdr` to `cbHdr + cbSize`
				eqnBody := make([]byte, cbSize)
				_, _ = reader.Seek(int64(cbHdr), io.SeekStart)
				_, _ = reader.Read(eqnBody)

				eqn = new(MTEFv5)
				eqn.reader = bytes.NewReader(eqnBody)
				_ = eqn.readRecord()
				_ = eqn.makeAST()
				return eqn, nil
			}

			return nil, err
		}
	}
	return nil, err
}


type RecordType uint8
type OptionType uint8

const (
	END            RecordType = 0
	LINE           RecordType = 1
	CHAR           RecordType = 2
	TMPL           RecordType = 3
	PILE           RecordType = 4
	MATRIX         RecordType = 5
	EMBELL         RecordType = 6
	RULER          RecordType = 7
	FONT_STYLE_DEF RecordType = 8
	SIZE           RecordType = 9
	FULL           RecordType = 10
	SUB            RecordType = 11
	SUB2           RecordType = 12
	SYM            RecordType = 13
	SUBSYM         RecordType = 14
	COLOR          RecordType = 15
	COLOR_DEF      RecordType = 16
	FONT_DEF       RecordType = 17
	EQN_PREFS      RecordType = 18
	ENCODING_DEF   RecordType = 19
	FUTURE         RecordType = 100
	ROOT           RecordType = 255
)

const (
	MtefOptNudge           OptionType = 0x08
	MtefOptCharEmbell      OptionType = 0x01
	MtefOptCharFuncStart   OptionType = 0x02
	MtefOptCharEncChar8    OptionType = 0x04
	MtefOptCharEncChar16   OptionType = 0x10
	MtefOptCharEncNoMtcode OptionType = 0x20
	MtefOptLineNull        OptionType = 0x01
	mtefOPT_LP_RULER       OptionType = 0x02
	MtefOptLineLspace      OptionType = 0x04
	MtefOptLpRuler         OptionType = 0x02
	MtefColorCmyk          OptionType = 0x01
	MtefColorSpot          OptionType = 0x02
	MtefColorName          OptionType = 0x04
	mtefCOLOR_CMYK         OptionType = 0x01
	mtefCOLOR_SPOT         OptionType = 0x02
	mtefCOLOR_NAME         OptionType = 0x04
)

const (
	fnTEXT     uint8 = 1
	fnFUNCTION uint8 = 2
	fnVARIABLE uint8 = 3
	fnLCGREEK  uint8 = 4
	fnUCGREEK  uint8 = 5
	fnSYMBOL   uint8 = 6
	fnVECTOR   uint8 = 7
	fnNUMBER   uint8 = 8
	fnUSER1    uint8 = 9
	fnUSER2    uint8 = 10
	fnMTEXTRA  uint8 = 11
	fnTEXT_FE  uint8 = 12
	fnEXPAND   uint8 = 22
	fnMARKER   uint8 = 23
	fnSPACE    uint8 = 24
)

type MtTabStop struct {
	next   *MtTabStop
	_type  int16
	offset int16
}

type MtRuler struct {
	nStops      int16
	tabStopList *MtTabStop
}

type MtLine struct {
	nudgeX     int16
	nudgeY     int16
	lineSpace  uint8
	null       bool
	ruler      *MtRuler
	objectList *MtObjList
}

type MtEmbell struct {
	next   *MtEmbell
	nudgeX int16
	nudgeY int16
	embell uint8
}

type MtChar struct {
	nudgeX   int16
	nudgeY   int16
	options  uint8
	typeface uint8
	//16-bit integer MTCode value
	mtcode uint16
	//8-bit font position
	bits8 uint8
	//16-bit integer font position
	bits16         uint16
	embellishments *MtEmbell
}

type MtEqnPrefs struct {
	sizes  []string
	spaces []string
	styles []byte
}

type MtSize struct {
	lsize uint8
	dsize uint8
}

type MtfontStyleDef struct {
	fontDefIndex uint8
	name         string
}

type MtfontDef struct {
	encDefIndex uint8
	name        string
}

type MtColorDefIndex struct {
	index uint8
}

type MtColorDef struct {
	values []uint8
	name   string
}

type MtObjList struct {
	next   *MtObjList
	tag    RecordType
	objPtr []MtObject
}

type MtTmpl struct {
	nudgeX     int16
	nudgeY     int16
	selector   uint8
	variation  uint16
	options    uint8
	objectList *MtObjList
}

type MtPile struct {
	nudgeX int16
	nudgeY int16
	halign uint8
	valign uint8

	//ruler可以不读，不影响后面字节错位，因为这个是一个完整的额外record数据
	ruler *MtRuler

	//objectList可以不读，不影响后面字节错位，因为这个是一个完整的额外record数据
	objectList *MtObjList
}

type MtMatrix struct {
	nudgeX int16
	nudgeY int16
	valign uint8
	h_just uint8
	v_just uint8

	rows uint8
	cols uint8

	//row_parts uint8
	//col_parts uint8

	//objectList可以不读，不影响后面字节错位，因为这个是一个完整的额外record数据
	objectList *MtObjList
}

type MtEmbellRd struct {
	options    uint8
	nudgeX     int16
	nudgeY     int16
	embellType uint8
}

type MtAST struct {
	tag      RecordType
	value    MtObject
	children []*MtAST
}

type MtObject interface{}

func (ast *MtAST) debug(indent int) {
	fmt.Printf("> %#v MtAST %#v\n", indent, ast)
	indent += 1
	for _, ele := range ast.children {
		ele.debug(indent)
	}
}

type SelectorType uint8

//Template selectors and variations:
const (
	//Fences (parentheses, etc.):
	//selector	symbol	description	class
	tmANGLE   SelectorType = 0 //	angle brackets	ParBoxClass
	tmPAREN   SelectorType = 1 //	parentheses	ParBoxClass
	tmBRACE   SelectorType = 2 //	braces (curly brackets)	ParBoxClass
	tmBRACK   SelectorType = 3 //	square brackets	ParBoxClass
	tmBAR     SelectorType = 4 //	vertical bars	ParBoxClass
	tmDBAR    SelectorType = 5 //	double vertical bars	ParBoxClass
	tmFLOOR   SelectorType = 6 //	floor brackets	ParBoxClass
	tmCEILING SelectorType = 7 //	ceiling brackets	ParBoxClass
	tmOBRACK  SelectorType = 8 //	open (white) brackets	ParBoxClass
	//variations	variation bits	symbol	description
	//0×0001	tvFENCE_L	left fence is present
	//0×0002	tvFENCE_R	right fence is present

	//Intervals:
	//selector	symbol	description	class
	tmINTERVAL SelectorType = 9 //	unmatched brackets and parentheses	ParBoxClass
	//variations	variation bits	symbol	description
	//0×0000	tvINTV_LEFT_LP	left fence is left parenthesis
	//0×0001	tvINTV_LEFT_RP	left fence is right parenthesis
	//0×0002	tvINTV_LEFT_LB	left fence is left bracket
	//0×0003	tvINTV_LEFT_RB	left fence is right bracket
	//0×0000	tvINTV_RIGHT_LP	right fence is left parenthesis
	//0×0010	tvINTV_RIGHT_RP	right fence is right parenthesis
	//0×0020	tvINTV_RIGHT_LB	right fence is left bracket
	//0×0030	tvINTV_RIGHT_RB	right fence is right bracket

	//Radicals (square and nth roots):
	//selector	symbol	description	class
	tmROOT SelectorType = 10 //	radical	RootBoxClass
	//variations	variation	symbol	description
	//0	tvROOT_SQ	square root
	//1	tvROOT_NTH	nth root

	//Fractions:
	//selector	symbol	description	class
	tmFRACT SelectorType = 11 //	fractions
	//variations	variation bits	symbol	description
	//0×0001	tvFR_SMALL	subscript-size slots (piece fraction)
	//0×0002	tvFR_SLASH	fraction bar is a slash
	//0×0004	tvFR_BASE	num. and denom. are baseline aligned

	//Over and Underbars:
	//selector	symbol	description	class
	tmUBAR SelectorType = 12 //	underbar	BarBoxClass
	tmOBAR SelectorType = 13 //	overbar	BarBoxClass
	//variations	variation bits	symbol	description
	//0×0001	tvBAR_DOUBLE	bar is doubled, else single

	//Arrows:
	//selector	symbol	description	class
	tmARROW SelectorType = 14 //	arrow	ArroBoxClass
	//variations	variation	symbol	description
	//0×0000	tvAR_SINGLE	single arrow
	//0×0001	tvAR_DOUBLE	double arrow
	//0×0002	tvAR_HARPOON	harpoon
	//0×0004	tvAR_TOP	top slot is present
	//0×0008	tvAR_BOTTOM	bottom slot is present
	//0×0010	tvAR_LEFT	if single, arrow points left
	//0×0020	tvAR_RIGHT	if single, arrow points right
	//0×0010	tvAR_LOS	if double or harpoon, large over small
	//0×0020	tvAR_SOL	if double or harpoon, small over large

	//Integrals (see Limit Variations):
	//selector	symbol	description	class
	tmINTEG SelectorType = 15 //	integral	BigOpBoxClass
	//variations	variation	symbol	description
	//0×0001	tvINT_1	single integral sign
	//0×0002	tvINT_2	double integral sign
	//0×0003	tvINT_3	triple integral sign
	//0×0004	tvINT_LOOP	has loop w/o arrows
	//0×0008	tvINT_CW_LOOP	has clockwise loop
	//0×000C	tvINT_CCW_LOOP	has counter-clockwise loop
	//0×0100	tvINT_EXPAND	integral signs expand

	//Sums, products, coproducts, unions, intersections, etc. (see Limit Variations):
	//selector	symbol	description	class
	tmSUM    SelectorType = 16 //	sum	BigOpBoxClass
	tmPROD   SelectorType = 17 //	product	BigOpBoxClass
	tmCOPROD SelectorType = 18 //	coproduct	BigOpBoxClass
	tmUNION  SelectorType = 19 //	union	BigOpBoxClass
	tmINTER  SelectorType = 20 //	intersection	BigOpBoxClass
	tmINTOP  SelectorType = 21 //	integral-style big operator	BigOpBoxClass
	tmSUMOP  SelectorType = 22 //	summation-style big operator	BigOpBoxClass

	//Limits (see Limit Variations):
	//selector	symbol	description	class
	tmLIM SelectorType = 23 //	limits	LimBoxClass
	//variations	variation	symbol	description
	//0	tvSUBAR	single underbar
	//1	tvDUBAR	double underbar

	//Horizontal braces and brackets:
	//selector	symbol	description	class
	tmHBRACE SelectorType = 24 //	horizontal brace	HFenceBoxClass
	tmHBRACK SelectorType = 25 //	horizontal bracket	HFenceBoxClass
	//variations	variation	symbol	description
	//0×0001	tvHB_TOP	slot is on the top, else on the bottom

	//Long division:
	//selector	symbol	description	class
	tmLDIV SelectorType = 26 //	long division	LDivBoxClass
	//variations	variation	symbol	description
	//0×0001	tvLD_UPPER	upper slot is present

	//Subscripts and superscripts:
	//selector	symbol	description	class
	tmSUB    SelectorType = 27 //	subscript	ScrBoxClass
	tmSUP    SelectorType = 28 //	superscript	ScrBoxClass
	tmSUBSUP SelectorType = 29 //	subscript and superscript	ScrBoxClass
	//variations	variation	symbol	description
	//0×0001	tvSU_PRECEDES	script precedes scripted item,

	//else follows
	//Dirac bra-ket notation:
	//selector	symbol	description	class
	tmDIRAC SelectorType = 30 //	bra-ket notation	DiracBoxClass
	//variations	variation	symbol	description
	//0×0001	tvDI_LEFT	left part is present
	//0×0002	tvDI_RIGHT	right part is present

	//Vectors:
	//selector	symbol	description	class
	tmVEC SelectorType = 31 //	vector	HatBoxClass
	//variations	variation	symbol	description
	//0×0001	tvVE_LEFT	arrow points left
	//0×0002	tvVE_RIGHT	arrow points right
	//0×0004	tvVE_UNDER	arrow under slot, else over slot
	//0×0008	tvVE_HARPOON	harpoon

	//Hats, arcs, tilde, joint status:
	//selector	symbol	description	class
	tmTILDE   SelectorType = 32 //	tilde over characters	HatBoxClass
	tmHAT     SelectorType = 33 //	hat over characters	HatBoxClass
	tmARC     SelectorType = 34 //	arc over characters	HatBoxClass
	tmJSTATUS SelectorType = 35 //	joint status construct	HatBoxClass

	//Overstrikes (cross-outs):
	//selector	symbol	description	class
	tmSTRIKE SelectorType = 36 //	overstrike (cross-out)	StrikeBoxClass
	//variations	variation	symbol	description
	//0×0001	tvST_HORIZ	line is horizontal, else slashes
	//0×0002	tvST_UP	if slashes, slash from lower-left to upper-right is present
	//0×0004	tvST_DOWN	if slashes, slash from upper-left to lower-right is present

	//Boxes:
	//selector	symbol	description	class
	tmBOX SelectorType = 37 //	box	TBoxBoxClass
	//variations	variation	symbol	description
	//0×0001	tvBX_ROUND	corners are round, else square
	//0×0002	tvBX_LEFT	left side is present
	//0×0004	tvBX_RIGHT	right side is present
	//0×0008	tvBX_TOP	top side is present
	//0×0010	tvBX_BOTTOM	bottom side is present
)

type EmbellType uint8

const (
	emb1DOT      EmbellType = 2
	emb2DOT      EmbellType = 3
	emb3DOT      EmbellType = 4
	emb1PRIME    EmbellType = 5
	emb2PRIME    EmbellType = 6
	embBPRIME    EmbellType = 7
	embTILDE     EmbellType = 8
	embHAT       EmbellType = 9
	embNOT       EmbellType = 10
	embRARROW    EmbellType = 11
	embLARROW    EmbellType = 12
	embBARROW    EmbellType = 13
	embR1ARROW   EmbellType = 14
	embL1ARROW   EmbellType = 15
	embMBAR      EmbellType = 16
	embOBAR      EmbellType = 17
	emb3PRIME    EmbellType = 18
	embFROWN     EmbellType = 19
	embSMILE     EmbellType = 20
	embX_BARS    EmbellType = 21
	embUP_BAR    EmbellType = 22
	embDOWN_BAR  EmbellType = 23
	emb4DOT      EmbellType = 24
	embU_1DOT    EmbellType = 25
	embU_2DOT    EmbellType = 26
	embU_3DOT    EmbellType = 27
	embU_4DOT    EmbellType = 28
	embU_BAR     EmbellType = 29
	embU_TILDE   EmbellType = 30
	embU_FROWN   EmbellType = 31
	embU_SMILE   EmbellType = 32
	embU_RARROW  EmbellType = 33
	embU_LARROW  EmbellType = 34
	embU_BARROW  EmbellType = 35
	embU_R1ARROW EmbellType = 36
	embU_L1ARROW EmbellType = 37
)

```

​		在此go文件目录下执行编译MathTypeConvert.go程序命令

```go
go build -buildmode=c-shared -o bin/MathTypeConvert-mac.so MathTypeConvert.go 
```

​		此时得到如下两个文件，MathTypeConvert-mac.so、MathTypeConvert-mac.h，这两个文件只能在mac环境下运行，如果需要部署到linux环境，见Mac编译linux环境步骤

![image-20210630113638025](/images/image-20210630113638025.png)

### windows编译windows可运行.so文件

​		windows上部署好golang环境，安装好gcc编译库，执行一下命令，得到.so、.h文件，本文不再赘述

```
go build -buildmode=c-shared -o bin/MathTypeConvert-mac.so MathTypeConvert.go 
```



### Mac m1编译linux可运行.so文件

​		如果是mac m1架构需要安装交叉编译linux的环境

```go
# brew 安装编译环境
brew tap messense/macos-cross-toolchains
brew install x86_64-unknown-linux-gnu
brew install x86_64-unknown-linux-gnu
```

​		更新path到.zshrc文件

```shell
#打开环境变量文件
vi .zshrc

#添加linux编译环境
export CC_x86_64_unknown_linux_gnu=x86_64-unknown-linux-gnu-gcc
export CXX_x86_64_unknown_linux_gnu=x86_64-unknown-linux-gnu-g++
export AR_x86_64_unknown_linux_gnu=x86_64-unknown-linux-gnu-ar
export CARGO_TARGET_X86_64_UNKNOWN_LINUX_GNU_LINKER=x86_64-unknown-linux-gnu-gcc

#更新.zshrc
source .zshrc
```

​		进入MathTypeConvert.go父目录执行编译命令

```go
CGO_ENABLED=1 GOOS=linux GOARCH=amd64 CC=x86_64-unknown-linux-gnu-gcc CC_FOR_TARGET=x86_64-unknown-linux-gnu-gcc go build -buildmode=c-shared -o bin/MathTypeConvert.so MathTypeConvert.go
```

​		此时将得到linux环境下运行的库文件，MathTypeConvert.so、MathTypeConvert.h，这两个文件是linux环境下可运行文件

![image-20210630114242543](/images/image-20210630114242543.png)

### 解决方案

​		此时可供java JNI调用的so、.h库文件已经存在，java已解析出mathType的二进制文件，接下来就是使用java调用so、.h库文件代码

java工程 pom引入JNI

```java
<dependency>
  <groupId>net.java.dev.jna</groupId>
  <artifactId>jna</artifactId>
  <version>5.8.0</version>
</dependency>
```

由于go程序接受字符串是goString类型，而java传参的是String类型，此处需要进行一次转换，新建GoString.java文件，此文件在调用传参时体现作用String latex = MathTypeToLatex.mathTypeTransformer.Convert(new GoString.ByValue(path))

```java
package com.spark.teaching.word.to.question.util.math;

import com.sun.jna.Structure;

import java.util.ArrayList;
import java.util.List;

/**
 * @author : liu heng
 * @description :
 * @date : 2021-06-26 11:17
 **/
public class GoString extends Structure {
    public String str;
    public long length;

    public GoString() {
    }

    public GoString(String str) {
        this.str = str;
        this.length = str.length();
    }

    @Override
    protected List<String> getFieldOrder() {
        List<String> fields = new ArrayList<>();
        fields.add("str");
        fields.add("length");
        return fields;
    }

    public static class ByValue extends GoString implements Structure.ByValue {
        public ByValue() {
        }

        public ByValue(String str) {
            super(str);
        }
    }

    public static class ByReference extends GoString implements Structure.ByReference {
        public ByReference() {
        }

        public ByReference(String str) {
            super(str);
        }
    }
}
```

​		新建mathType转换latex程序运行类MathTypeToLatex.java、及MathTypeConfig.java，MathTypeConfig.java主要用于加载.so、.h文件

``` java
package com.spark.teaching.word.to.question.util.math;


import com.google.common.io.ByteStreams;
import com.sun.jna.Library;
import com.sun.jna.Native;
import com.sun.jna.Structure;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.io.*;

/**
 * @author : liu heng
 * @description : mathType转latex
 * @date : 2021-06-26 14:38
 **/
public class MathTypeToLatex {
    private static final Logger LOGGER = LoggerFactory.getLogger(MathTypeToLatex.class);
    public static MathTypeTransformer mathTypeTransformer;

    private static String lib;

    static {
        try {
            String os = System.getProperty("os.name").toLowerCase();
            String mathLibSoName;
            String mathLibHName;
            if (os.contains("mac")) {
                // mac平台
                mathLibSoName = "MathTypeConvert-mac.so";
                mathLibHName = "MathTypeConvert-mac.h";
            } else if (os.contains("windows") || os.contains("Windows")) {
                // windows平台
                mathLibSoName = "MathTypeConvert-win.so";
                mathLibHName = "MathTypeConvert-win.h";
            } else {
                // linux平台
                mathLibSoName = "MathTypeConvert.so";
                mathLibHName = "MathTypeConvert.h";
            }
            lib = loadLib("mathLib", mathLibSoName);
            loadLib("mathLib", mathLibHName);
            LOGGER.info("MathTypeToLatex->从jar包中读取数据");
            LOGGER.info("MathTypeToLatex->lib:{}", lib);
        } catch (Exception e) {
            LOGGER.warn("MathTypeToLatex->发生异常:{}", e.toString());
            lib = null;
        }
    }

    public void loadMathTypeConvert() {
        try {
            LOGGER.info("loadMathTypeConvert->开始加载mathType转换库");
            mathTypeTransformer = Native.load(lib, MathTypeTransformer.class);
            LOGGER.info("loadMathTypeConvert->加载mathType转换库成功:{}", mathTypeTransformer);
        } catch (Exception e) {
            LOGGER.info("loadMathTypeConvert->加载mathType转换库失败:{}", e.toString());
        }
    }

    private static String loadLib(String path, String name) throws IOException {
        InputStream in = null;
        try {
            // path 是自定义路径，name 是so 文件名
            String binPath = MathTypeToLatex.class.getClassLoader().getResource("bin").getFile();
            String lib = binPath + "/MathTypeConvert.so";
            in = MathTypeToLatex.class.getResourceAsStream("/bin/" + name);
            LOGGER.info("MathTypeToLatex->lib:{},文件流是否存在:{}", lib, in != null);
            if (in == null) {
                in = new FileInputStream(lib);
            }
            // 临时文件路径
            String tmpPath = System.getProperty("user.dir") + "/" + path;
            // 创建新文件
            File fileOutDic = new File(tmpPath);
            if (!fileOutDic.exists()) {
                fileOutDic.mkdirs();
            }

            File fileOut = new File(tmpPath + name);
            if (!fileOut.exists()) {
                fileOut.createNewFile();
            }

            OutputStream out = new FileOutputStream(fileOut);
            ByteStreams.copy(in, out);
            in.close();
            out.close();
            // 调用load 方法加载
            return fileOut.getAbsolutePath();
        } catch (Exception e) {
            LOGGER.error("loadLib->加载so 库失败!{}", e.toString());
        } finally {
            if (in != null) {
                in.close();
            }
        }
        return null;
    }

    public interface MathTypeTransformer extends Library {
        /**
         * mathType转latex
         *
         * @param filePath
         * @return
         */
        String Convert(Structure.ByValue filePath);
    }
		
  	// 测试运行，oleObject-rId6.bin为测试文件，您可以更改为自己的mathType二进制文件，将一份含有MathType的word,更改为.zip结尾解压，在word目录下的embeddings文件中可以找到
    public static void main(String[] args) {
        LOGGER.info("MathTypeToLatex->Java says: about to call Go ..");
        new MathTypeToLatex().loadMathTypeConvert();
        String binPath = MathTypeToLatex.class.getClassLoader().getResource("bin").getFile();
        binPath = binPath + "/oleObject-rId6.bin";
        String total = mathTypeTransformer.Convert(new GoString.ByValue(binPath));
        LOGGER.info("MathTypeToLatex->Java says: result is {}", total);
    }
}

```

```java
package com.spark.teaching.word.to.question.config;

import com.spark.teaching.word.to.question.util.math.MathTypeToLatex;
import org.springframework.context.annotation.Bean;
import org.springframework.stereotype.Component;

/**
 * @author : liu heng
 * @description :
 * @date : 2021-06-27 11:44
 **/
@Component
public class MathTypeConfig {
    @Bean
    public MathTypeToLatex getMathTypeToLatex(){
        MathTypeToLatex mathTypeToLatex = new MathTypeToLatex();
        mathTypeToLatex.loadMathTypeConvert();
        return mathTypeToLatex;
    }
}
```



运行MathTypeToLatex.java中的main函数，得到如下结果

```
Java says: result is $$ -1\frac { 3 } { 5 } $$
```

**恭喜您，相信您看到这里已经成功的执行转mathType为latex公式**



### 参考文献

- 基于go语言解析mathType为latex-https://github.com/zhexiao/mtef-go
- java调用golang生成动态库-https://studygolang.com/articles/13646
- mac m1交叉编译linux环境.so库-https://github.com/messense/homebrew-macos-cross-toolchains
- java使用poi读写word中的公式-https://blog.csdn.net/qq_18219457/article/details/98963136

