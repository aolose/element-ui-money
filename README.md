# element-ui-money
基于element ui 样式的货币格式输入框,自动补全分隔符

#### 属性：
fix 保留位数

e.g

```
<money :fixed='2' v-model="Budget">
    <template slot="append">KUSD</template>
</money>
```

![](https://github.com/aolose/element-ui-money/blob/master/eg.png?raw=true)
