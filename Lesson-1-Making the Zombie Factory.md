# CryptoZombies(Lesson 1)
## Part 1

#### Contracts and pragma version
##### Contracts
- Solidity's code is encapsulated in contracts. A contract is the fundamental building block of Ethereum applications — all variables and functions belong to a contract, and this will be the starting point of all your projects.
```sh
contract HelloWorld {

}
```
##### Version Pragma
- All solidity source code should start with a "version pragma" — a declaration of the version of the Solidity compiler this code should use. 

```sh
pragma solidity ^0.4.19;
contract HelloWorld {

}
```

#### State Variables & Integers
##### state variables
- State variables are permanently stored in contract storage. This means they're written to the Ethereum blockchain. Think of them like writing to a DB.

##### Un-signed integers
- The uint data type is an unsigned integer, meaning its value must be non-negative. There's also an int data type for signed integers.
- In Solidity, uint is actually an alias for uint256, a 256-bit unsigned integer. You can declare uints with less bits — uint8, uint16, uint32, etc..
```sh
pragma solidity ^0.4.19;
contract ZombieFactory {
    uint dnaDigits = 16;
}
```

#### Math Operations
- Addition: x + y
- Subtraction: x - y,
- Multiplication: x * y
- Division: x / y
- Modulus / remainder: x % y
- exponential operator (x ** y)
```sh
pragma solidity ^0.4.19;
contract ZombieFactory {
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
}
```
#### Structs
Sometimes you need a more complex data type. For this, Solidity provides structs:
```sh
struct Person {
  uint age;
  string name;
}
```
Structs allow you to create more complicated data types that have multiple properties.
```sh
pragma solidity ^0.4.19;
contract ZombieFactory {
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
    struct Zombie {
        string name;
        uint dna;
    }
}
```

#### Arrays
When you want a collection of something, you can use an array. There are two types of arrays in Solidity: fixed arrays and dynamic arrays:
// Array with a fixed length of 2 elements: [ uint[2] fixedArray; ]
// another fixed Array, can contain 5 strings: [ string[5] stringArray; ]
// a dynamic Array - has no fixed size, can keep growing: [ uint[] dynamicArray; ]
##### Public Arrays
You can declare an array as public, and Solidity will automatically create a getter method for it. The syntax looks like:

**Person[] public people;**

Other contracts would then be able to read (but not write) to this array. So this is a useful pattern for storing public data in your contract.
```sh
pragma solidity ^0.4.19;
contract ZombieFactory {
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
    struct Zombie {
        string name;
        uint dna;
    }
    Zombie[] public zombies;
}
```
#### Function Declarations
A function declaration in solidity looks like the following:
```sh
function eatHamburgers(string _name, uint _amount) {

}
```
```sh
pragma solidity ^0.4.19;
contract ZombieFactory {
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
    struct Zombie {
        string name;
        uint dna;
    }
    Zombie[] public zombies;
    function createZombie(string _name, uint _dna) {
        
    }
}
```

#### Working With Structs and Arrays
##### Creating New Structs
create new Zombies and add them to our zombies array.
```sh
// create a New Person:
Zombie satoshi = Zombie("satoshi", 123232);
// Add that person to the Array:
zombies.push(satoshi);
```
**OR**
```sh
zombies.push(Zombie(_name, _dna)); // in one line
```

```sh
pragma solidity ^0.4.19;
contract ZombieFactory {
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
    struct Zombie {
        string name;
        uint dna;
    }
    Zombie[] public zombies;
    function createZombie(string _name, uint _dna) {
        zombies.push(Zombie(_name, _dna));
    }
}
```

#### Private / Public Functions
In Solidity, functions are public by default. This means anyone (or any other contract) can call your contract's function and execute its code.
Obviously this isn't always desireable, and can make your contract vulnerable to attacks. Thus it's good practice to mark your functions as private by default, and then only make public the functions you want to expose to the world.
```sh
pragma solidity ^0.4.19;
contract ZombieFactory {
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
    struct Zombie {
        string name;
        uint dna;
    }
    Zombie[] public zombies;
    function _createZombie(string _name, uint _dna) private {
        zombies.push(Zombie(_name, _dna));
    }
}
```

#### More on Functions

##### Return values
In Solidity, the function declaration contains the type of the **return value**
```sh
string greeting = "What's up dog";
function sayHello() public returns (string) {
  return greeting;
}
```
##### Function modifiers
The above function doesn't actually change state in Solidity — e.g. it doesn't change any values or write anything.
So in this case we could declare it as a view function, meaning it's only viewing the data but not modifying it:
```sh
function sayHello() public view returns (string) {
```
Solidity also contains pure functions, which means you're not even accessing any data in the app. Consider the following:
```sh
function _multiply(uint a, uint b) private pure returns (uint) {
  return a * b;
}
```
This function doesn't even read from the state of the app — its return value depends only on its function parameters. So in this case we would declare the function as pure.
```sh
pragma solidity ^0.4.19;
contract ZombieFactory {
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
    struct Zombie {
        string name;
        uint dna;
    }
    Zombie[] public zombies;
    function _createZombie(string _name, uint _dna) private {
        zombies.push(Zombie(_name, _dna));
    } 
    function _generateRandomDna(string _str) private view returns (uint) {
        
    }
}
```

#### Keccak256 and Typecasting
We want our _generateRandomDna function to return a (semi) random uint. How can we accomplish this?
Ethereum has the hash function keccak256 built in, which is a version of SHA3. A hash function basically maps an input string into a random 256-bit hexidecimal number. A slight change in the string will cause a large change in the hash.
It's useful for many purposes in Ethereum, but for right now we're just going to use it for pseudo-random number generation.
Example:
```sh
//6e91ec6b618bb462a4a6ee5aa2cb0e9cf30f7a052bb467b0ba58b8748c00d2e5
keccak256("aaaab");
//b1f078126895a1424524de5321b339ab00408010b7cf0e6ed451514981e58aa9
keccak256("aaaac");
```
As you can see, the returned values are totally different despite only a 1 character change in the input.
**Note:** Secure random-number generation in blockchain is a very difficult problem. Our method here is insecure, but since security isn't top priority for our Zombie DNA, it will be good enough for our purposes.
 ##### Type casting
 Sometimes you need to convert between data types. Take the following example:
```sh
uint8 a = 5;
uint b = 6;
// throws an error because a * b returns a uint, not uint8:
uint8 c = a * b; 
// we have to typecast b as a uint8 to make it work:
uint8 c = a * uint8(b);
```
```sh
pragma solidity ^0.4.19;
contract ZombieFactory {
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
    struct Zombie {
        string name;
        uint dna;
    }
    Zombie[] public zombies;
    function _createZombie(string _name, uint _dna) private {
        zombies.push(Zombie(_name, _dna));
    } 
    function _generateRandomDna(string _str) private view returns (uint) {
        uint rand = uint(keccak256(_str));
        return rand % dnaModulus;
    }
}
```

#### Final output of part 1

```sh
pragma solidity ^0.4.19;
contract ZombieFactory {
    // declare our event here
    event NewZombie(uint zombieId, string name, uint dna);
    
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
    struct Zombie {
        string name;
        uint dna;
    }
    Zombie[] public zombies;
    function _createZombie(string _name, uint _dna) private {
        uint id = zombies.push(Zombie(_name, _dna)) - 1;
        // and fire it here
        NewZombie(id, _name, _dna);
    } 
    function _generateRandomDna(string _str) private view returns (uint) {
        uint rand = uint(keccak256(_str));
        return rand % dnaModulus;
    }
    function createRandomZombie(string _name) public {
        uint randDna = _generateRandomDna(_name);
        _createZombie(_name, randDna);
    }
}
```










