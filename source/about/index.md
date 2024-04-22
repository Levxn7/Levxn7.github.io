---
title: 背灯和月就花荫
date: 2021-05-21 16:02:19
---

<script src="https://cdn.bootcss.com/jquery/1.8.3/jquery.js"></script>

<style>
    td {
        border: solid #000 1px;
        background-color: darkgray;
        width: 30px;
        height: 30px;
        font-size: 12px
    }
    .pink {
        background-color: pink;
    }
</style>
<!-- <p style="text-align:center;padding-left:20px">已是十年踪迹十年心</p> -->

<div style="text-align:center;padding-left:20px">
<p>已是十年踪迹十年心</p>
<p style="padding-top:20px">学习之余来玩个游戏吧</p>
<p style="padding-top:20px">格子里面有20颗炸弹喔,来找出他们吧~</p>
<!-- <input id="zx-col" type="number" placeholder="输入行数吧"/> -->

<!-- <input id="zx-row" type="number" placeholder="输入列数吧" /> -->

<!-- <input id="zx-boom" type="number" placeholder="设置雷数吧" /> -->

<button id="comfirm">
    启动扫雷!
</button>

<p id="result"></p>
</div>


<table id="table" style="width: auto!important">
</table>

<script type="text/javascript">


var zxcol;
var zxrow;
var zxboom;
var pinkNum = 0
var pinkArr = []
var over = false
// 生成棋盘
$("#comfirm").click(function () {
    document.getElementById('result').innerText = '';
    pinkNum = 0
    pinkArr = []
    over = false
    // zxcol = $("#zx-col").val();
    // zxrow = $("#zx-row").val();
    // zxboom = $("#zx-boom").val();

    zxcol = 10;
    zxrow = 10;
    zxboom = 20;
    var sum = zxcol * zxrow;


    // 设置格子
    var stri = "";
    var array = [];
    for (var i = 0; i < zxcol; i++) {
        var strj = "";
        for (let j = 0; j < zxrow; j++) {
            let temp = i + "zx" + j;
            array.push(temp);
            strj += "<td id='" + i + "zx" + j + "' class='notBoom'></td>";
        }
        stri += "<tr id='tr" + i + "'>" + strj + "</tr>";
    }
    $("#table").html(stri);


    // 随机数
    var integerArray = getRandomNumber(sum, zxboom);
    var boomArray = [];
    // console.log(integerArray);
    for (let i = 0; i < integerArray.length; i++) {
        const ele = integerArray[i];
        var item = array[ele]
        boomArray.push(item);
        document.getElementById(item).setAttribute("class", "isBoom")
    }
});
// 取 n 个随机整数
function getRandomNumber(end, n) {
    var arr = [];
    for (var i = 0; i < n; i++) {
        var number = Math.floor(Math.random() * end);
        if (arr.indexOf(number) < 0) {
            arr.push(number);
        } else {
            i--;
        }
    }
    return arr;
}

// 踩雷死亡
$("body").on("click", ".isBoom", function () {
    if (over) return
    $(".isBoom").css("background", "red");
    document.getElementById('result').innerText = '踩雷了喔~';
    over = true
})

//右键插旗事件
$("table").bind("contextmenu", function () {
    return false;
})
$("body").on("mousedown", "td", function (e) {
    // console.log(e.which);
    //右键为3
    if (3 == e.which) {
        // console.log(document.getElementById(this.id).className);
        var currentClass = document.getElementById(this.id).className;
        if (currentClass.indexOf("pink") > 0) {
            let a = currentClass.indexOf("pink")
            let temp = currentClass.replace(" pink", "");
            // console.log(temp);
            document.getElementById(this.id).setAttribute("class", temp);
            pinkNum --
            pinkArr.splice(a, 1)

        } else {
            var newClass = currentClass + " pink"
            document.getElementById(this.id).setAttribute("class", newClass);
            pinkNum ++
            pinkArr.push(currentClass)

        }
        if (pinkNum == zxboom && pinkArr.indexOf("notBoom") < 0) {
            // alert('你赢啦~')
            document.getElementById('result').innerText = '你赢啦~';
            over = true
        }
    }
})



// 左键游戏
$("body").on("click", ".notBoom", function () {
    if (over) return
    var result = 0;
    var currentClass = document.getElementById(this.id).className;
    if (currentClass.indexOf("pink") > -1) {
        let temp = currentClass.replace(" pink", "");
        document.getElementById(this.id).setAttribute("class", temp);
        pinkNum --
        pinkArr.splice(pinkArr.indexOf(temp), 1)
    }
    if (pinkNum == zxboom && pinkArr.indexOf("notBoom") < 0) {
        document.getElementById('result').innerText = '你赢啦1~';
        over = true
    }

    let currentId = this.id;
    let idArr = currentId.split("zx");
    // let currentIdNum = currentId.replace("td", "");
    // let zxtop, zxbottom, zxleft, zxlight, zxtleft, zxtlight, zxbleft, zxblight;
    // console.log(idArr);
    let neighborBoxArr = nearBox(idArr, zxcol, zxrow);
    let boomNum = neighborBoomNum(neighborBoxArr);

    showBoomNum(boomNum, currentId, idArr, zxcol, zxrow);
    // console.log(boomNum);


    // $("#table").html(boomNum);
    // console.log(boomNum);
    $(".notBoom").each(function () {
        if (this.style.backgroundColor.indexOf("lightgray") > -1) {
            result ++;
        }
    });

    if (result == zxcol * zxrow - zxboom) {
        document.getElementById('result').innerText = '你赢啦2~';
        over = true
    }

})

// 返回周围8个格子坐标
function nearBox(arr, col, row) {
    let coordinateA = Number(arr[0])
    let coordinateB = Number(arr[1])

    let zxtop, zxbottom, zxleft, zxlight, zxtleft, zxtlight, zxbleft, zxblight;

    zxtop = (coordinateA - 1) + "zx" + coordinateB;
    zxbottom = (coordinateA + 1) + "zx" + coordinateB;
    zxleft = coordinateA + "zx" + (coordinateB - 1);
    zxlight = coordinateA + "zx" + (coordinateB + 1);
    zxtleft = (coordinateA - 1) + "zx" + (coordinateB - 1);
    zxtlight = (coordinateA - 1) + "zx" + (coordinateB + 1);
    zxbleft = (coordinateA + 1) + "zx" + (coordinateB - 1);
    zxblight = (coordinateA + 1) + "zx" + (coordinateB + 1);
    // console.log(zxtleft, zxtop, zxtlight, zxleft, zxlight, zxbleft, zxbottom, zxblight);

    let neighbourArr = [];
    if (coordinateA == 0) {
        neighbourArr = [zxleft, zxlight, zxbleft, zxbottom, zxblight];

        if (coordinateB == 0) {
            neighbourArr = [zxlight, zxbottom, zxblight];
        }
        if (coordinateB == row - 1) {
            neighbourArr = [zxleft, zxbleft, zxbottom];
        }

    } else if (coordinateA == col - 1) {
        neighbourArr = [zxtleft, zxtop, zxtlight, zxleft, zxlight];

        if (coordinateB == 0) {
            neighbourArr = [zxtop, zxtlight, zxlight];
        }
        if (coordinateB == row - 1) {
            neighbourArr = [zxtleft, zxtop, zxleft];
        }

    } else {
        neighbourArr = [zxtleft, zxtop, zxtlight, zxleft, zxlight, zxbleft, zxbottom, zxblight];

        if (coordinateB == 0) {
            neighbourArr = [zxtop, zxtlight, zxlight, zxbottom, zxblight];
        }
        if (coordinateB == row - 1) {
            neighbourArr = [zxtleft, zxtop, zxleft, zxbleft, zxbottom];
        }
    }

    // console.log(neighbourArr);
    return neighbourArr;

}
// 返回周围8个格子的炸弹数
function neighborBoomNum(neighborBoxArr) {
    let boomNum = 0
    for (let i = 0; i < neighborBoxArr.length; i++) {
        const element = neighborBoxArr[i];
        if (document.getElementById(element).className.indexOf("isBoom") > -1) {
            boomNum += 1;
        }
    }
    return boomNum;

}
function showBoomNum(boomNum, currentId, arr, col, row) {
    if (boomNum == 0) {
        document.getElementById(currentId).style.backgroundColor = "lightgray";

        //检测周围4个格子炸弹
        let neighborBoxArr = nearFourBox(arr, col, row);
        for (let i = 0; i < neighborBoxArr.length; i++) {
            const ele = neighborBoxArr[i];
            //  console.log(ele);
            if (document.getElementById(ele).style.backgroundColor.indexOf("lightgray") < 0) {
                document.getElementById(ele).click();
            }
        }
    }
    // $("#table").html(boomN
    if (boomNum > 0) {
        document.getElementById(currentId).innerText = boomNum;
        document.getElementById(currentId).style.backgroundColor = "lightgray";
    }

}
// 返回周围4个格子坐标
function nearFourBox(arr, col, row) {
    let coordinateA = Number(arr[0])
    let coordinateB = Number(arr[1])

    let zxtop, zxbottom, zxleft, zxlight

    zxtop = (coordinateA - 1) + "zx" + coordinateB;
    zxbottom = (coordinateA + 1) + "zx" + coordinateB;
    zxleft = coordinateA + "zx" + (coordinateB - 1);
    zxlight = coordinateA + "zx" + (coordinateB + 1);

    let neighbourArr = [];
    if (coordinateA == 0) {
        neighbourArr = [zxleft, zxlight, zxbottom];

        if (coordinateB == 0) {
            neighbourArr = [zxlight, zxbottom];
        }
        if (coordinateB == row - 1) {
            neighbourArr = [zxleft, zxbottom];
        }

    } else if (coordinateA == col - 1) {
        neighbourArr = [zxtop, zxleft, zxlight];

        if (coordinateB == 0) {
            neighbourArr = [zxtop, zxlight];
        }
        if (coordinateB == row - 1) {
            neighbourArr = [zxtop, zxleft];
        }

    } else {
        neighbourArr = [zxtop, zxleft, zxlight, zxbottom];

        if (coordinateB == 0) {
            neighbourArr = [zxtop, zxlight, zxbottom];
        }
        if (coordinateB == row - 1) {
            neighbourArr = [zxtop, zxleft, zxbottom];
        }
    }

    return neighbourArr;

}
</script>
