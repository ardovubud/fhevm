// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "./FHEVMEnvironment.sol";
import "./FHEVMRelayer.sol";

contract ConfidentialContract is FHEVMEnvironment, FHEVMRelayer {
    ebool private encryptedFlag;
    euint256 private encryptedCounter;
    eaddress private encryptedOwner;

    constructor(ebool memory initialFlag, euint256 memory initialCounter, eaddress memory owner) {
        require(initialized(), "Encrypted variables must be initialized");
        encryptedFlag = initialFlag;
        encryptedCounter = initialCounter;
        encryptedOwner = owner;
    }

    function increment() external {
        require(FHE.isSenderAllowed(msg.sender), "Sender not allowed");
        if (FHE.asEbool(encryptedFlag)) {
            encryptedCounter = FHE.add(encryptedCounter, FHE.asEuint256(1));
        }
    }

    function setFlag(ebool memory flag) external {
        require(FHE.isSenderAllowed(msg.sender), "Sender not allowed");
        encryptedFlag = flag;
    }

    function getEncryptedCount() external view returns (euint256 memory) {
        return encryptedCounter;
    }

    function allowAccess(address user) external {
        require(msg.sender == FHE.decryptAddress(encryptedOwner), "Only owner can allow access");
        FHE.allow(user);
    }
}
