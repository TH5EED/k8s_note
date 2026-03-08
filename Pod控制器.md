控制器用来==不断地==确保集群当前状态与期望状态（清单的spec部分）保持一致
![](assets/Pod控制器/file-20260308144423943.png)
# RC和RS
rc会创建期望数量的pod，并自动将其labels与自己的selector相同
