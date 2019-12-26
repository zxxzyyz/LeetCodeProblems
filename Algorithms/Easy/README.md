# Easy Problems

### Find Numbers with Even Number of Digits
Given an array nums of integers, return how many of them contain an even number of digits.
```
Example 1:
Input: nums = [12,345,2,6,7896]
Output: 2
Explanation: 
12 contains 2 digits (even number of digits). 
345 contains 3 digits (odd number of digits). 
2 contains 1 digit (odd number of digits). 
6 contains 1 digit (odd number of digits). 
7896 contains 4 digits (even number of digits). 
Therefore only 12 and 7896 contain an even number of digits.

Example 2:
Input: nums = [555,901,482,1771]
Output: 1 
Explanation: 
Only 1771 contains an even number of digits.

Constraints:
1 <= nums.length <= 500
1 <= nums[i] <= 10^5
```
##### Solution
```Javascript
Solution 1:
function findNumbers(nums) {
    let result = 0;
    for (let i = 0; i < nums.length; i++) {
        let digit = 0;
        let num = nums[i];
        while(num) {
            num = Math.floor(num / 10);
            digit++;
        }
        if (digit % 2 === 0) result++;
    }
    return result;
};

Solution 2:
function findNumbers(nums) {
    return nums.reduce((result, num) => (Math.floor(Math.log10(num)) + 1) % 2 === 0 ? result += 1 : result, 0)
};

Solution 3:
function findNumbers(nums) {
  return nums.reduce((result, num) => num.toString().length % 2 === 0 ? result += 1 : result , 0)
};
```

### Defanging an IP Address
Given a valid (IPv4) IP address, return a defanged version of that IP address.
A defanged IP address replaces every period "." with "[.]".
```
Example 1:
Input: address = "1.1.1.1"
Output: "1[.]1[.]1[.]1"

Example 2:
Input: address = "255.100.50.0"
Output: "255[.]100[.]50[.]0"

Constraints:
The given address is a valid IPv4 address.
```

##### Solution
```
Solution 1:
const defangIPaddr = address => address.split(".").join("[.]");

Solution 2:
function defangIPaddr(address) {
    return address.replace(/\./g, "[.]");
}
```
