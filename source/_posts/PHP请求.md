---
title: PHP外部请求
date: 2017-01-13 22:38:15
update: 2017-01-13 22:38:15
categories: PHP
tags: [php]
---
### 1.php请求
<!-- more -->
```php
	public function UOPStorage($dataList) {
        // 构造参数
        $param = Array();
        // receivePush
        $receivePush = Array();
        $trace = Array();
        $parcelDat = Array();
        foreach ($dataList as $data) {
            $parcelId = $data['f_parcel_id'];
            $where = array('f_parcel_id' => $parcelId);
            $parcelInfo = new \Home\Model\ParcelInfoModel();
            $lastTime = $parcelInfo->getParcelArriveTime($where);
            $lastTime = $lastTime['f_last_time'];
            $time = date("Y年n月j日 ag:i:s", strtotime($lastTime));
            $time = str_replace('am', "上午", $time);
            $time = str_replace('pm', "下午", $time);
            $parcelDat['scanTime'] = date("Y-m-d H:i:s", time());
            $parcelDat['context'] = "联系方式：" . $data['f_tel'];
            if ($data['f_type'] == 1) {
                $reqType = array('f_storage_status' => '0');
                $parcelDat['scanType'] = "0";// 请求类型 在途
            } elseif ($data['f_type'] == 2) {
                $reqType = array('f_arrive_status' => '0');
                $parcelDat['scanType'] = "15";// 请求类型 签收
                $parcelDat['context'] = "context";
            } else {
                $reqType = array('f_rejection_status' => '0');
                $parcelDat['scanType'] = "0";// 请求类型 在途
            }
            $parcelDat['siteName'] = $data['f_station_name'];
            $parcelDat['mobile'] = $data['f_tel'];
            $trace['trace'] = $parcelDat;
            $receivePush['billCode'] = $data['f_mail_no'];
            $receivePush['traces'] = $trace;
            $param['receivePush'] = $receivePush;
            $this->doPostUOP($data['f_parcel_id'], json_encode($param), $reqType);
        }
    }

    public function doPostUOP($parcelId, $param, $reqType) {
        // 测试
        // $UOPeceiveUrl = "http://123.123.123.123/com.uc5.main/a/a.action";
        $secretKey = "ab0d3b38bb2";
        //$secretKey = "6a8b866e7eb4ad";
        $partner = "041";
        $serviceName = "receive_track";
        $dataType = "json";
        // 拼接加密字符串
        $data = Array();
        $data[0] = "dataType" . "=" . $dataType;
        $data[1] = "param" . "=" . $param;
        $data[2] = "partner" . "=" . $partner;
        $data[3] = "serviceName" . "=" . $serviceName;
        $comma_separated = implode("&", $data);
        // 加密最后结果
        $dataSign = md5($comma_separated . $secretKey);
        $Request_Data['dataSign'] = $dataSign;
        $Request_Data['partner'] = $partner;
        $Request_Data['serviceName'] = $serviceName;
        $Request_Data['dataType'] = $dataType;
        $Request_Data['param'] = $param;
        // 请求UOP平台
        $postdata = http_build_query($Request_Data);
        $options = array(
            'http' => array(
                'method' => 'POST',
                'header' => 'Content-type:application/x-www-form-urlencoded',
                'content' => $postdata,
                'timeout' => 5 * 60 // 超时时间（单位:s）
            )
        );
        $context = stream_context_create($options);
        $result = file_get_contents($UOPeceiveUrl, true, $context);
        $xml = simplexml_load_string('<?xml version="1.0" encoding="gbk"?>' . $result);
        $re = (string)$xml->returnCode;
        // 请求成功更新包裹状态
        if ($re == "SUCCESSFUL") {
            // 更新包裹状态
            print_r($re);
            foreach($reqType as $key=>$val){
                $reqType[$key] = 1;
            }
        } else {
            foreach($reqType as $key=>$val){
                $reqType[$key] = 2;
            }
        }
        $parcelInfo = new \Home\Model\StationAbutmentLogModel();
        $parcelInfo->updateExpressData($parcelId, $reqType);
    }
```
