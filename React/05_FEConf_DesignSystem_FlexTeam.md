> FEConf에서 발표해주신 flex 사의 이소영님의 "디자인 시스템, 형태를 넘어서"를 듣고 정리한 내용이다.



## 기본 동작을 보장한다.

- 
- 적은 기본 기능
  비슷한 기능을 매번 정의해야 한다. 파편화로 이어질 수 있는 문제다.

>  확장성, 일관성을 기본 정의해야 한다.



기본동작을 보장한다.

Scroll영역

SelectComponent 위에는 고정 아래는 스크롤.



키보드 탐색기능



> 기본동작은 보장하되, 기본동작이 아닌 것은 정의하지 않는다. 여러 상황에 유연하게 대응 한다.
>
> 어떤 상황에서는 변경하거나 제거해야 할 수 있기 때문이다.



## 최소한의 제약만 가진다.





## 예제 구체화

### CheckBox / Radio



flex에서는 모든 컴포넌트를 Compound Components로 사용한다.

Trigger Component의 경우 다음과 같은 코드를 사용한다.

```jsx
<Select>
	<Select.Trigger />
  
  <Select.Content>
  	<Select.Item />
    <Select.Group />
    <Select.Separator />
  </Select.Content>
</Select>
```



Container역할을 하는 Select 컴포넌트의 경우 context를 내려주기위한 provider로 감싼다.

이 때, 

```jsx
function Select() {
	return (
		<SelectProvider
      trigger={triggerElement}
      onTriggerChange={setTriggerElement}
      >
    	{children}
    </SelectProvider>
	)
}
```



`Select.Content`에서 context를 사용한다.

```jsx
function SelectContent() {
  const ctx = useSelectContext();
  
  return (...)
}
```





