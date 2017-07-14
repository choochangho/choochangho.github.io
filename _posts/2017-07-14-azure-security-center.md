---
layout: post
title:  "Azure 에서 DefaultResourceGroup-XXX 와 securitydata ResourceGroup"
categories: azure security portal
date:   2017-07-14 10:10
permalink: /archivers/azure-mysterious-group
---

최근에 생성하지 않은 ResourceGroup 이 발견되어 그 내용을 정리해 본다.

![MysteriouseResourceGroup]({{ base }}/assets/images/2017-07-14/resource_group_list.png)

위와 같이 DefaultResourceGroup-EJP 와 securitydata 라는 ResourceGroup 이 생성된 것을 볼 수 있다.

DefaultResource-EJP 를 클릭 해보면 아래와 같은 Log Analytics 를 확인 할 수 있다.

![MysteriouseResourceGroup]({{ base }}/assets/images/2017-07-14/default_resource_group.png)

조금 더 들어가 보자.

![MysteriouseResourceGroup]({{ base }}/assets/images/2017-07-14/default_resource_group1.png)

로그 검색을 클릭하면 아래와 같은 화면을 볼 수 있다.

![MysteriouseResourceGroup]({{ base }}/assets/images/2017-07-14/default_resource_group2.png)

여기서 '수집된 모든 데이터' 를 클릭하면 VM 에서 수집된 로그기록을 볼 수 있다.

![MysteriouseResourceGroup]({{ base }}/assets/images/2017-07-14/default_resource_group3.png)

즉, securitydata 라는 리소스 그룹의 저장소 계정에 위와 같은 로그가 저장되고 있었다.

다시, '보안센터 > 보안 정책'에서 해당 구독을 클릭하면 데이터 수집을 On/Off 할 수 있다.

![MysteriouseResourceGroup]({{ base }}/assets/images/2017-07-14/default_resource_group4.png)

securitydata 리소스 그룹은 자동으로 생성이 되며 별도로 설정에서 Off 시키지 않는 한 로그가 수집되고 저장 된다는 것을 알 수 있다.

다만, 이 때 비용(Standard Tier)이 발생 할 수 있으므로 주의 하여야 한다.

![MysteriouseResourceGroup]({{ base }}/assets/images/2017-07-14/default_resource_group5.png)