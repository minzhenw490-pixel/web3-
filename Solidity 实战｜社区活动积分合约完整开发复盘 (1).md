# Solidity 实战｜社区活动积分合约完整开发复盘

指令：第一次尝试完整写一个 Solidity 智能合约，记录一下这次学习过程。

这次做的是一个社区活动积分系统。

1️⃣我先给出prompt：

请用 Solidity 写一个活动积分合约，要求：

1\. 管理员可以给指定地址增加积分（用于活动发奖）。

2\. 用户自己可以签到（每天限 1 次，加 10 分）。

3\. 用户可以查询自己的总积分。

4\. 管理员可以查询排行榜前 10 名（方便公示）。

5\. 合约要包含防刷机制（比如签到间隔必须大于 24 小时）。

请附上详细注释。”用于签到奖励和用户贡献积分记录。

交给AI后，他会生成代码

```Plain Text
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

// 活动积分记录合约
contract CommunityPoints {

    // 管理员地址（部署合约的人）
    address public admin;

    // 用户地址 => 积分数量
    mapping(address => uint256) private points;


    // 构造函数，在部署时记录管理员地址
    constructor() {
        admin = msg.sender;
    }


    // 管理员权限校验修饰器
    modifier onlyAdmin() {
        require(msg.sender == admin, "Only admin can operate");
        _;
    }


    // 管理员给用户发放积分
    function grantPoints(address user, uint256 amount) external onlyAdmin {
        // 用户地址不能为空
        require(user != address(0), "Invalid user address");

        // 发放积分不能为0
        require(amount > 0, "Amount must be greater than zero");

        // 增加用户积分
        points[user] += amount;
    }


    // 管理员扣除用户积分
    function deductPoints(address user, uint256 amount) external onlyAdmin {
        // 用户地址不能为空
        require(user != address(0), "Invalid user address");

        // 扣除积分不能为0
        require(amount > 0, "Amount must be greater than zero");

        // 用户积分必须足够扣除
        require(points[user] >= amount, "Insufficient points");

        // 减少用户积分
        points[user] -= amount;
    }


    // 查询指定用户积分
    function getUserPoints(address user) external view returns (uint256) {
        // 返回用户当前积分
        return points[user];
    }


    // 查询用户积分总额
    function getTotalPoints() external view returns (uint256 total) {
        // 遍历统计需要额外存储用户列表，当前版本不实现
        return total;
    }
}
```

2️⃣但在检查代码的时候，我发现了一些实际项目里必须考虑的问题：

1、权限控制

积分属于平台核心数据，如果任何人都可以调用加分函数，整个积分体系会直接失控。

所以增加了管理员权限限制，只有合约部署者可以执行积分增减操作。

2、Event 事件日志

之前只关注了“数据有没有变化”，忽略了链上操作记录。

加入 Event 后，每一次积分增加、扣除、签到都会留下链上日志，方便后续查询、运营对账和问题追踪。

3、防刷机制

如果签到没有限制，用户可以无限调用签到函数刷积分。

因此增加了 24 小时签到间隔限制，通过记录用户上次签到时间，避免重复领取。

经过我修改过后的代码：

```Plain Text
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

// 活动积分记录合约
contract CommunityPoints {

    // 管理员地址（合约部署者）
    address public admin;

    // 用户地址 => 积分数量
    mapping(address => uint256) private points;

    // 用户地址 => 上次签到时间
    mapping(address => uint256) private lastCheckInTime;


    // 每次签到奖励积分
    uint256 public constant CHECKIN_REWARD = 10;


    // 发放积分事件
    event PointsGranted(address indexed user, uint256 amount);

    // 扣除积分事件
    event PointsDeducted(address indexed user, uint256 amount);

    // 签到事件
    event CheckIn(address indexed user, uint256 amount, uint256 timestamp);



    // 构造函数，部署时记录管理员地址
    constructor() {
        admin = msg.sender;
    }


    // 管理员权限校验
    modifier onlyAdmin() {
        require(msg.sender == admin, "Only admin can operate");
        _;
    }



    // 管理员给用户发放积分
    function grantPoints(address user, uint256 amount) external onlyAdmin {

        // 用户地址不能为空
        require(user != address(0), "Invalid user address");

        // 发放积分不能为0
        require(amount > 0, "Amount must be greater than zero");


        // 增加用户积分
        points[user] += amount;


        // 记录发放日志
        emit PointsGranted(user, amount);
    }



    // 管理员扣除用户积分
    function deductPoints(address user, uint256 amount) external onlyAdmin {

        // 用户地址不能为空
        require(user != address(0), "Invalid user address");

        // 扣除积分不能为0
        require(amount > 0, "Amount must be greater than zero");


        // 用户积分必须足够扣除
        require(points[user] >= amount, "Insufficient points");


        // 扣除积分
        points[user] -= amount;


        // 记录扣除日志
        emit PointsDeducted(user, amount);
    }



    // 用户签到领取积分
    function checkIn() external {

        // 获取用户上次签到时间
        uint256 lastTime = lastCheckInTime[msg.sender];


        // 限制24小时内不能重复签到
        require(
            block.timestamp >= lastTime + 1 days,
            "Already checked in within 24 hours"
        );


        // 更新签到时间
        lastCheckInTime[msg.sender] = block.timestamp;


        // 增加签到积分
        points[msg.sender] += CHECKIN_REWARD;


        // 记录签到日志
        emit CheckIn(
            msg.sender,
            CHECKIN_REWARD,
            block.timestamp
        );
    }



    // 查询指定用户积分
    function getUserPoints(address user) external view returns (uint256) {

        // 返回用户积分
        return points[user];
    }



    // 查询用户积分总额
    function getTotalPoints() external view returns (uint256 total) {

        // 当前mapping无法遍历所有用户，所以暂不统计
        return total;
    }
}
```

3️⃣ 打开 Remix，粘贴最终代码

打开 https://remix\.ethereum\.org，创建 Points\.sol 文件，把修改后的最终代码粘贴进去，按 ⌘ \+ S 保存。



4️⃣ 编译合约

点击左侧竖排第 2 个图标（Solidity Compiler，像字母 S），选择编译器版本 0\.8\.24，点击 Compile Points\.sol，出现绿色 ✅ 表示编译通过。



5️⃣ 部署合约到 Remix VM

切换到第 3 个图标（Deploy \& Run Transactions，菱形标志），Environment 选择 Remix VM \(Cancun\)，点击蓝色 Deploy 按钮。

下方出现 Deployed Contracts，显示合约地址，说明部署成功。



6️⃣ 测试签到功能

在 Deployed Contracts 下方，点击函数下拉菜单选择 checkIn，点击 transact 发起签到交易。

底部出现 绿色条，说明交易成功 ✅

点击绿色条查看详情，可以看到交易哈希（Transaction Hash）



7️⃣ 测试防刷机制

再点一次 checkIn → transact，这次出现 红色报错，提示“24小时未到”，说明防刷机制生效了 ✅



8️⃣ 查询积分

选择 getUserPoints，点击 call，下方显示 10，确认积分已正确累计 ✅

以前学习只停留在看教程，这次是第一次**全流程亲手实战**：从提需求、生成代码、自查漏洞、手动优化，再到编译部署、功能自测，全程自己踩坑、自己修复。

也真正悟出来：AI 只能给基础 Demo，**区块链开发最核心的是安全和细节**。权限管控、防刷机制、链上日志，这些看似简单的点，却是项目能不能落地的关键。

完整跑通一遍真的收获超大，比单纯看理论扎实太多，后续继续深耕合约实战！



> （注：部分内容可能由 AI 生成）
