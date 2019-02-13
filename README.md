
pragma solidity ^0.4.15;

contract Game{
    mapping(string=>mapping(string=>int)) payoffMatrix;
    
    //Represents an ethereum address in 20 bytes value. player1 automatically assigned to 0, and player2 to 1.
    address player1;
    address player2;
    
    //Creating public variables atomatically creates a getter function that allows 
    //to get the values from external source
    string public player1Selection;
    string public player2Selection;
    
    //contructor function, created only once
    
    function Game(){
       payoffMatrix ['rock']['rock'] = 0;
       payoffMatrix ['rock']['paper'] = 2;
       payoffMatrix ['rock']['scissor'] = 1;
       payoffMatrix ['scissor']['rock'] = 2;
       payoffMatrix ['scissor']['paper'] = 1;
       payoffMatrix ['scissor']['scissor'] = 0;
       payoffMatrix ['paper']['rock'] = 1;
       payoffMatrix ['paper']['paper'] = 0;
       payoffMatrix ['paper']['scissor'] = 2;
    }
    //payable allows to accept ether, otherwise will fail
    function register() payable notRegisteredYet {
        
        if (player1 == 0)
        //msg.sender is the special global variable equal to the ethereum address whoever call the function
        player1 = msg.sender;
        else if(player2 == 0)
        player2 = msg.sender;
    }
    
    modifier notRegisteredYet{
    if (msg.sender == player1 || msg.sender == player2)
    revert();
    else
    _;
    }
    modifier sentEnoughCash(uint amount){
        if(msg.value < amount)
        revert();
        else
        _;
    }
    function play (string choice) returns (int w){
        if (msg.sender==player1)
        player1Selection = choice;
        else if(msg.sender==player2)
        player2Selection = choice;
        if (bytes(player1Selection).length != 0 && bytes(player2Selection).length != 0){
            int winner = payoffMatrix[player1Selection][player2Selection];
            if (winner ==1)
                player1.transfer(this.balance);
                else if(winner ==2)
                player2.transfer(this.balance);
                else {
                    player1.transfer(this.balance/2);
                    player2.transfer(this.balance);
                }
                //reset choices and addresses to "" and 0
                player1Selection = "";
                player2Selection = "";
                player1 = 0;
                player2 = 0;
                return winner;
                
        }
            else
                return -1;
    }
    
    //Getter Function - Used to get data from the Blockchain, but doesn't write on it.
    
    function getContractBalance() constant returns (uint amount){
        return this.balance;
    }
    
    function checkIfPlayer1() constant returns (bool v) {
        return msg.sender == player1;
    }
    function checkIfPlayer2() constant returns (bool v){
        return msg.sender == player2;
    }
    
}
