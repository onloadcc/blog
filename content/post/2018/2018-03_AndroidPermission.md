+++
title = "Android权限申请"
description = "Android权限申请，流程"
date = "2018-04-01"
categories = ["Android"]
tags = ["Android","Permission"]
thumbnail = ""
+++

<p></p>

　　安卓在6.0	(23)之后，增加了权限管理。权限分类三类：普通权限(Normal)，签名权限(Signature)和危险权限(Dangerous)。前两个在安装应用时系统会自动授予，危险权限必须需要用户手动授权。

　　所有的危险权限都属于权限组。如果对同一组内的一个权限有授权过，则访问同一组内的其它权限时系统会自动授予权限，不用再次请求用户。

 <!--more-->

## Dangerous permissions and permission groups

<p class="table-caption" id="permission-groups">
  <strong>Table 1.</strong> Dangerous permissions and permission groups.
</p>

<table>
<tr>
  <th scope="col">Permission Group</th>
  <th scope="col">Permissions</th>
</tr>

<tr>
  <td><code><a href="https://developer.android.com/reference/android/Manifest.permission_group.html#CALENDAR">CALENDAR</a></code></td>
  <td>
    <ul>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#READ_CALENDAR">READ_CALENDAR</a></code>
      </li>
    </ul>
    <ul>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#WRITE_CALENDAR">WRITE_CALENDAR</a></code>
      </li>
    </ul>
  </td>
</tr>

<tr>
  <td><code><a href="https://developer.android.com/reference/android/Manifest.permission_group.html#CAMERA">CAMERA</a></code></td>
  <td>
    <ul>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#CAMERA">CAMERA</a></code>
      </li>
    </ul>
  </td>
</tr>

<tr>
  <td><code><a href="https://developer.android.com/reference/android/Manifest.permission_group.html#CONTACTS">CONTACTS</a></code></td>
  <td>
    <ul>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#READ_CONTACTS">READ_CONTACTS</a></code>
      </li>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#WRITE_CONTACTS">WRITE_CONTACTS</a></code>
      </li>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#GET_ACCOUNTS">GET_ACCOUNTS</a></code>
      </li>
    </ul>
  </td>
</tr>

<tr>
  <td><code><a href="https://developer.android.com/reference/android/Manifest.permission_group.html#LOCATION">LOCATION</a></code></td>
  <td>
    <ul>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#ACCESS_FINE_LOCATION">ACCESS_FINE_LOCATION</a></code>
      </li>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#ACCESS_COARSE_LOCATION">ACCESS_COARSE_LOCATION</a></code>
      </li>
    </ul>
  </td>
</tr>

<tr>
  <td><code><a href="https://developer.android.com/reference/android/Manifest.permission_group.html#MICROPHONE">MICROPHONE</a></code></td>
  <td>
    <ul>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#RECORD_AUDIO">RECORD_AUDIO</a></code>
      </li>
    </ul>
  </td>
</tr>

<tr>
  <td><code><a href="https://developer.android.com/reference/android/Manifest.permission_group.html#PHONE">PHONE</a></code></td>
  <td>
    <ul>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#READ_PHONE_STATE">READ_PHONE_STATE</a></code>
      </li>
      <li>
          <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#READ_PHONE_NUMBERS">READ_PHONE_NUMBERS</a></code>
      </li>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#CALL_PHONE">CALL_PHONE</a></code>
      </li>
      <li>
          <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#ANSWER_PHONE_CALLS">ANSWER_PHONE_CALLS</a></code> (<em>must request at runtime</em>)
      </li>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#READ_CALL_LOG">READ_CALL_LOG</a></code>
      </li>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#WRITE_CALL_LOG">WRITE_CALL_LOG</a></code>
      </li>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#ADD_VOICEMAIL">ADD_VOICEMAIL</a></code>
      </li>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#USE_SIP">USE_SIP</a></code>
      </li>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#PROCESS_OUTGOING_CALLS">PROCESS_OUTGOING_CALLS</a></code>
      </li>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#ANSWER_PHONE_CALLS">ANSWER_PHONE_CALLS</a></code>
      </li>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#READ_PHONE_NUMBERS">READ_PHONE_NUMBERS</a></code>
      </li>
    </ul>
  </td>
</tr>

<tr>
  <td><code><a href="https://developer.android.com/reference/android/Manifest.permission_group.html#SENSORS">SENSORS</a></code></td>
  <td>
    <ul>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#BODY_SENSORS">BODY_SENSORS</a></code>
      </li>
    </ul>
  </td>
</tr>

<tr>
  <td><code><a href="https://developer.android.com/reference/android/Manifest.permission_group.html#SMS">SMS</a></code></td>
  <td>
    <ul>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#SEND_SMS">SEND_SMS</a></code>
      </li>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#RECEIVE_SMS">RECEIVE_SMS</a></code>
      </li>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#READ_SMS">READ_SMS</a></code>
      </li>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#RECEIVE_WAP_PUSH">RECEIVE_WAP_PUSH</a></code>
      </li>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#RECEIVE_MMS">RECEIVE_MMS</a></code>
      </li>
    </ul>
  </td>
</tr>

<tr>
  <td>
    <code><a href="https://developer.android.com/reference/android/Manifest.permission_group.html#STORAGE">STORAGE</a></code>
  </td>
  <td>
    <ul>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#READ_EXTERNAL_STORAGE">READ_EXTERNAL_STORAGE</a></code>
      </li>
      <li>
        <code><a href="https://developer.android.com/reference/android/Manifest.permission.html#WRITE_EXTERNAL_STORAGE">WRITE_EXTERNAL_STORAGE</a></code>
      </li>
    </ul>
  </td>
</tr>

</table>


## 权限申请流程，请对照[官方介绍](https://developer.android.com/guide/topics/permissions/index.html)，可以翻译中文。

安利一张图作下流程介绍(也包含小米申请流程)：

<iframe id="embed_dom" name="embed_dom" frameborder="0" style="display:block;width:100%; height:65vw;" src="https://www.processon.com/embed/5ab50deae4b02cee4ce8ca90"></iframe>



## 使用时当然需要作封装了，推荐几个库

[PermissionsDispatcher－使用注解方便申请](https://github.com/permissions-dispatcher/PermissionsDispatcher)   

[easypermissions](https://github.com/googlesamples/easypermissions)  

[一行代码搞定Android6.0动态权限授权、权限管理-将权限申请封装到单独Activity中，方便调用。不再每个界面都写`onRequestPermissionsResult`](https://github.com/dfqin/PermissionGrantor)  




