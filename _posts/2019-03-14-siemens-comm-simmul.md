---
title: "SIEMENS PLC communication programing with C# S7.netplus"
header:
  overlay_color: "#333"
categories:
  - C# programing
tags:
  - IT
  - SIEMENS
  - PLC communication
  - S7.net
last_modified_at: 2019-03-14T16:19:00-00:00
---

지멘스 PLC 통신 프로그래밍 

현재 SIEMENS S7-300 PLC 계열 장비를 사용중입니다.
얼마전 간단히 PLC Value 몇개를 주기적으로 모니터링 해야 하는 경우가 생겼습니다.
C#을 주로 사용하고 있어서 C#으로 SIEMENS PLC와 통신할 방법을 찾다가 알게 되었습니다.


본 포스팅은 C# S7.Net 라이브러리 사용법과 간단히 값을 가져오는 시뮬레이션 개발 내용입니다.

먼저 아래 사이트를 참조 하였습니다.

{% capture notice-2 %}
* **S7.Net GitHub :** [https://github.com/S7NetPlus/s7netplus](https://github.com/S7NetPlus/s7netplus)
* **S7.Net Manual :** [https://github.com/S7NetPlus/s7netplus/blob/develop/Documentation/Documentation.pdf](https://github.com/S7NetPlus/s7netplus/blob/develop/Documentation/Documentation.pdf)
{% endcapture %}

<div class="notice">
  {{ notice-2 | markdownify }}
</div>

## S7.Netplus 라이브러리 설치 - Visual studio

먼저 S7.Netplus 라이브러를 설치해야 합니다. NuGet package로 쉽게 설치 할 수 있습니다.

> 버전 확인! S7.Net 0.2 버전 이후부터는 .Net Framework 4.5.2 이상 설치가 필요합니다. 서버에 설치 된 .Net Framework 버전을 확인 하십시요.

![S7netplus install]({{ site.url }}{{ site.baseurl }}/assets/images/20181203_154601.png){: .align-center}


## Simmulator 개발

먼저 시뮬레이터 화면 구성은 아래와 같습니다. 

![Simmulatior layout]({{ site.url }}{{ site.baseurl }}/assets/images/20190314_164355.png){: .align-center}



### PLC 접속 부분 구현

우선 CPU 타입부터 확인 합니다. CPU 타입은 라이브러리에 아래와 같이 정의 되어 있습니다.
```ruby
namespace S7.Net
{
    public enum CpuType
    {
        S7200 = 0,
        S7300 = 10,
        S7400 = 20,
        S71200 = 30,
        S71500 = 40
    }
}
```

기본설정은 FormLoad시에 아래와 같이 구현합니다. (이후 IP address, Rack, Slot 번호는 접속하고자 하는 PLC H/W 구성에 맞게 넣어 주십시요.)
```ruby
private void FormMain_Load(object sender, EventArgs e)
{
    try
    {
        cbCpuType.DataSource = Enum.GetNames(typeof(CpuType));
        cbCpuType.SelectedIndex = 1; // Default is S7-300

        btnMWrite.Enabled = false;
    }
    catch (Exception ex)
    {
        MessageBox.Show(this, ex.Message, "informaiton", MessageBoxButtons.OK, MessageBoxIcon.Information);
    }
}
```

Connect 버튼 함수 내용은 아래와 같습니다.
```ruby
private void btnConnect_Click(object sender, EventArgs e)
{
    try
    {
        CpuType cpu = (CpuType)Enum.Parse(typeof(CpuType), cbCpuType.SelectedValue.ToString());
        string ip = tbIPaddress.Text;
        short rack = short.Parse(tbRack.Text);
        short slot = short.Parse(tbSlot.Text);

        plc = new Plc(cpu, ip, rack, slot);
        plc.Open();
        btnConnect.Enabled = false;
    }
    catch (PlcException pe)
    {
        MessageBox.Show(this, pe.Message, "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
    catch (Exception ex)
    {
        MessageBox.Show(this, ex.Message, "informaiton", MessageBoxButtons.OK, MessageBoxIcon.Information);
    }
}
```

추가로 FormClosing때 PLC 접속도 끈어줍시다.
```ruby
private void FormMain_FormClosing(object sender, FormClosingEventArgs e)
{
    try
    {
        if (plc != null)
        {
            plc.Close();
        }
    }
    catch (Exception ex)
    {
        MessageBox.Show(this, ex.Message, "informaiton", MessageBoxButtons.OK, MessageBoxIcon.Information);
    }
}
```


### PLC Value 읽기

> 값을 읽어 오기 위해서는 자료형에 유의 해야 합니다. 자료형에 맞는 네이밍 규칙이 있습니다.
> 예) INT형은 DB220.DBW100, REAL형은 DB220.DBD100, BOOL형은 DB220.DBX100.1

```ruby
    /*
    * Read a single variable / Write a single variable
    * This method reads a single variable from the plc, by parsing the string and returning the correct result. 
    * While this is the easiest method to get started, is very inefficient because the driver sends a TCP request for every variable.
    * 
    * ushort result = (ushort)plc.Read("DB1.DBW0"); //Read Word
    * short result = ((ushort)plc.Read("DB1.DBW0")).ConvertToShort();  //Read Int
    * uint result = (uint)plc.Read("DB1.DBD40");   //Read DWord
    * int result = ((uint)plc.Read("DB1.DBD60")).ConvertToInt();  //Read Dint
    * double result = ((uint)plc.Read("DB1.DBD40")).ConvertToDouble(); //Read Real
    * bool result = (bool)plc.Read(vaddress);  //Read bit(bool)
    * 
    * public byte[] ReadBytes(DataType dataType, int db, int startByteAdr, int count)
    */
```

Read 버튼 구현은 아래와 같습니다.
```ruby
private void btnMRead_Click(object sender, EventArgs e)
{
    try
    {
        if (plc.IsConnected)
        {
            string vaddress = tbAddress.Text;

            if (rbReal.Checked == true)
            {
                double result = ((uint)plc.Read(vaddress)).ConvertToFloat();
                tbPV.Text = string.Format("{0}", result.ToString());
            }
            if (rbInt.Checked == true)
            {
                short result = ((ushort)plc.Read(vaddress)).ConvertToShort();
                tbPV.Text = string.Format("{0}", result.ToString());
            }
            if (rbDInt.Checked == true)
            {
                int result = ((uint)plc.Read(vaddress)).ConvertToInt();
                tbPV.Text = string.Format("{0}", result.ToString());
            }
            if (rbBit.Checked == true)
            {
                bool result = (bool)plc.Read(vaddress);
                tbPV.Text = string.Format("{0}", result.ToString());
            }
            if (rbString.Checked == true)
            {
                int db = Convert.ToInt32(tbDB.Text);
                int startByteAdr = Convert.ToInt32(tbDBW.Text);                        
                int count = Convert.ToInt32(tbCount.Text);                         
                byte[] byteArray = plc.ReadBytes(DataType.DataBlock, db, startByteAdr, count);
                string result = S7.Net.Types.String.FromByteArray(byteArray);
                tbPV.Text = string.Format("{0}", result.ToString());
            }
        }
    }
    catch (Exception ex)
    {
        MessageBox.Show(this, ex.Message, "informaiton", MessageBoxButtons.OK, MessageBoxIcon.Information);
    }
}
```


### PLC Value 쓰기

쓰는 기능은 필요치 않아서... 없습니다. 쿨럭.

읽는 포인트가 많지 않다면 1초 주기로 계속 돌려도 서버 퍼포먼스나 처리 속도에서 전혀 문제가 없는 듯 합니다.

문의 사항 있으시면, 댓글 남겨주십시요.
