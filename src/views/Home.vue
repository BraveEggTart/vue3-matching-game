<template>
	<div class="home">

		<div class="home-title">
			月野天星炉石友谊比赛流程工具
		</div>

		<div class="top-tip border" v-if="tree.level === 0">
			匹配均使用计算机进行随机匹配, 保证公平
		</div>

		<div class="form border">
			<div class="handle-set">
				<div>
					当前模式为:
					<select v-model="mode" @change="modeChange">
						<option :value="8">8</option>
						<option :value="16">16</option>
						<option :value="16">32</option>
					</select>
					人模式
				</div>
			</div>
			<div class="user-list">
				<div class="user-item" v-bind:key="(item, index)" v-for="(item, index) in userList">
					<input v-model="item.name" placeholder="请输入选手名称"/>
				</div>
			</div>

			<div class="handle-btn">
				<button @click="submitHandle">提交名单</button>
				<button @click="resetHandle">重置表单</button>
			</div>
		</div>
		<div class="border">
			<tree class="tree" :tree="tree" />
		</div>

		<div class="border warn">
			温馨提示:
			<br />1. 为了避免<span>(小金人)</span>手贱点了刷新导致数据丢失, 在每次提交名单后, 做了保存处理, 刷新后将恢复到上次提交时的数据
			<br />2. 每次点击"提交表单"按钮即会进行重新排序
			<br />3. 我们的口号是: 打倒<span>斯过一</span>
			<div class="author">作者: 英勇无比的芝士蛋挞</div>
		</div>


	</div>
</template>

<script>
/* eslint-disable */
import tree from '@/components/tree'
export default {
	name: 'Home',
	components: {tree},
	data() {
		return {
			userList: [],
			sortList: [],
			tree: {},
			mode: 16
		}
	},
	created() {
    console.log("start------")
		const userList = localStorage.getItem('userList');
		const tree = localStorage.getItem('tree');
		const mode = localStorage.getItem('mode');
		if (userList && tree) {
			this.userList = JSON.parse(userList)
			this.tree = JSON.parse(tree)
			this.mode = JSON.parse(mode)
		} else {
			this.resetHandle()
		}
	},
	methods: {
		addHandle() {
			this.userList.push({name: ''})
		},
		delHandle(index) {
			this.userList.splice(index, 1)
		},
		modeChange() {
			this.resetHandle()
		},
		resetHandle() {
			this.userList = [];
			this.tree = {};
			for (let i = 0; i < this.mode; i++) {
				this.userList.push({name: ''})
			}
		},
		submitHandle() {

			if (this.tree.children && !window.confirm('确定提交吗? 将清空下方之前的晋级图!')) {
				return
			}

			const arr = this.userList.filter(item => item.name !== '');

			if(arr.length <= this.mode * 2 / 3) {
				return alert('至少需要超过当前模式的2/3人数才能创建')
			}
			localStorage.setItem('userList', JSON.stringify(this.userList));
			localStorage.setItem('mode', JSON.stringify(this.mode));

			// 空缺位置
			let vacancy = this.mode - arr.length;
			// 补位
			const list = [...arr, ...new Array(vacancy).fill({name: ''})]
			// 排序
			function sort(arr) {
				return arr.sort(() => {
					return Math.random() - 0.5
				})
			}
			this.sortList = sort(list)

			this.setTree()
		},
		setTree() {
			let index = 0;
			const sortList = this.sortList;
			function each(children, step, tier) {

				if (step > tier) return
				for (let i = 0; i < 2; i++) {
					let obj = {
						name: '',
						level: step,
						children: []
					}
					if (step === tier) {
						obj.name = sortList[index].name;
						index++
					}

					children.push(obj)
					each(children[i].children, step + 1, tier)
				}
			}
			let data = {
				name: '',
				level: 0,
				children: []
			}
			const tier = {
				8: 3,
				16: 4,
				32: 5
			}
			each(data.children, 1, tier[this.mode])
			this.tree = data
			localStorage.setItem('tree', JSON.stringify(this.tree));
		}
	}
}
</script>

<style lang="scss">
.home {
	padding: 20px;

	.home-title {
		font-size: 30px;
		text-align: center;
		padding-bottom: 20px;

	}

	.top-tip {
		font-size: 28px;
		color: red;
	}
	.border {
		padding: 15px;
		border: 1px solid #42b983;
		margin-bottom: 15px;
		width: auto;
		.tree {

			overflow-y: auto;
		}
	}
	.author {
		font-size: 14px;
		text-align: right;
		color: #2c3e50;
	}
	.warn {
		color: red;
		font-size: 18px;
		span {
			color: green;
		}
	}

	.form {



		.handle-set {
			display: flex;
			margin-bottom: 10px;
			padding-bottom: 10px;
			border-bottom: 1px solid #ccc;
			button {
				margin-right: 30px;
			}
			select {
				font-size: 18px;
				width: 80px;
			}
		}

		.user-list {
			display: flex;
			flex-wrap: wrap;

			.user-item {
				display: flex;
				align-items: center;
				margin: 10px 10px 0 0;

				input {
					width: 120px;
					margin: 0 10px 0 0;
				}
			}
		}

		.handle-btn {
			margin-top: 20px;

			button {
				font-size: 20px;
				margin-right: 15px;
			}
		}
	}

}

</style>
