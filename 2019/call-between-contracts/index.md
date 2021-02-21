# Solidity-合约间调用


之前的实验合约间的调用没有成功，这次就仔细地研究一下合约间地调用机制。分为两种情况

1. 调用者和被调用者在一个sol文件中
2. 调用者和被调用者在不同的sol文件中

本文提到的合约调用方法的实质是抽象合约的使用。

### 同sol文件的智能合约调用

下面的智能合约中，Main和Add两个合约定义在一个Main.sol文件中，可以同时编译，然后逐个部署。

```js
pragma solidity ^0.5.0;


contract Main {
  Add add;
  
  constructor(address _m) public {
     add = Add(_m);
  }
  
  function Addnumber() public view returns (uint) {
    return add.add5(10);
  }
}

contract Add {
  function add5(uint s) public pure returns (uint){
      return 5+s;
  }
}
```

以使用Remix为例，点击编译按钮编译Main.sol文件，将会同时编译Main和Add两个合约。

![compile](https://picped-1301226557.cos.ap-beijing.myqcloud.com/68472178-e6294d00-025a-11ea-8b4b-41a53b471c18.png)

 然后首先部署Add合约，因为Main合约的部署需要Add的合约地址作为参数。切换到部署和运行选项卡，选择Add合约，点击`Deploy`，成功部署后，复制合约地址。

![deploy simple Add ](https://picped-1301226557.cos.ap-beijing.myqcloud.com/68472256-0e18b080-025b-11ea-9a24-e324c82cd7b5.png)

然后重新选择Main合约，填入Add合约地址作为参数，点击部署按钮。

![deploy simple Main ](https://picped-1301226557.cos.ap-beijing.myqcloud.com/68472285-1a047280-025b-11ea-8bb5-a1fb5c65574e.png)

测试合约间调用，由合约内容可知，Main合约中的Addnumber函数调用了Add合约的add5函数，传入参数为10，得到的结果应为15。展开左侧的`Deployed Contracts`，点击Addnumber进行调用，结果如下。

![call test](https://picped-1301226557.cos.ap-beijing.myqcloud.com/68472220-f5a89600-025a-11ea-9d2a-b39c8e39a810.png)

### 不同sol文件的智能合约调用

这一次我们测试不同sol文件的智能合约调用，来一个复杂一点的，两个合约分别是Add.sol和Main.sol。

Add.sol使用了一个结构体来定义数值，并通过映射定义查找表来寻找这个值。文件中定义了两个函数，numRegister用来向表中添加数值，addValue用来将从表中查到的指定值+5返回。之所以用这个结构是因为我们的项目里用到了，这里来测试一下可不可行。

```js
pragma solidity ^0.5.0;

contract Add {
    struct Num{
        uint value;
    }
    mapping(uint => Num) public lookupTable;
    
    function numRegister(uint key, uint _value) public {
        lookupTable[key].value = _value;
    }
    
    function addValue(uint key) public view returns (uint) {
        return lookupTable[key].value + 5;
    }

}
```

Main.sol没有多大变化

```js
pragma solidity ^0.5.0;

contract Main {
  Add add;
  
  constructor(address _m) public {
     add = Add(_m);
  }
  
  function Addnumber() public view returns (uint) {
    return add.addValue(5);
  }
}

contract Add {
      function addValue(uint key) public view returns (uint);
}
```

仍然是先编译部署Add合约，部署后调用numRegister函数写入数值5，并调用addValue函数测试返回。

![deploy comlex Add](https://picped-1301226557.cos.ap-beijing.myqcloud.com/68472316-2d174280-025b-11ea-96a6-51af8dd4fd4c.png)

接着编译部署Main合约，复制Add合约地址作为初始化参数，部署后调用Addnumber函数测试

![deploy comlex Main](https://picped-1301226557.cos.ap-beijing.myqcloud.com/68472331-399b9b00-025b-11ea-933f-8c03ff95c55a.png)

### 总结

合约内的调用方法是相同的，都要先实例化，然后传入被调合约地址，接着才能调用。而写在不同sol文件中时，需要额外声明被调合约的抽象合约，有些文章中说使用`call`，`callcode`或`delegatecall`，但并不建议，因为这三个函数都是非常底层的函数，破坏了类型的安全，只能作为最后的手段使用。

详细的解释参考了[StackExchange-Calling function from deployed contract](https://ethereum.stackexchange.com/questions/9733/calling-function-from-deployed-contract)
