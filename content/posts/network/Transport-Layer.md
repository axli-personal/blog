---
title: "Transport Layer"
date: 2022-07-09T12:00:00+08:00
summary: "Transport Layer"
---

## Congestion Control

### Cause

Retransmission: finite buffer, timeout.

Upstream transmission **capacity wasted** for packets lost downstream.

![2](https://oos.axlis.cn/blog/network/2.png)

## Compute RTO

> RTO: retransmission timeout
> 
> SRTT: smoothed round-trip time
> 
> RTTVAR: round-trip time variation

When the first RTT measurement R is made:

```
SRTT = R
RTTVAR = R / 2
RTO = SRTT + K * RTTVAR
```

When a subsequent RTT measurement R' is made:

```
RTTVAR = (1 - beta) * RTTVAR + beta * |SRTT - R'|
SRTT = (1 - alpha) * SRTT + alpha * R'
```

The value of SRTT used in the update to RTTVAR is its value before updating SRTT itself.

Suggested: `k = 4, alpha = 1 / 8, beta = 1 / 4`.

Reference: [RFC6298](https://www.rfc-editor.org/rfc/rfc6298.html)