<script lang="ts">
	const BLOCK_SIZE = 24;
	const GROUP_SIZE = 6;
	const BASE64_TABLE_BINARY = [
		'000000',
		'000001',
		'000010',
		'000011',
		'000100',
		'000101',
		'000110',
		'000111',
		'001000',
		'001001',
		'001010',
		'001011',
		'001100',
		'001101',
		'001110',
		'001111',
		'010000',
		'010001',
		'010010',
		'010011',
		'010100',
		'010101',
		'010110',
		'010111',
		'011000',
		'011001',
		'011010',
		'011011',
		'011100',
		'011101',
		'011110',
		'011111',
		'100000',
		'100001',
		'100010',
		'100011',
		'100100',
		'100101',
		'100110',
		'100111',
		'101000',
		'101001',
		'101010',
		'101011',
		'101100',
		'101101',
		'101110',
		'101111',
		'110000',
		'110001',
		'110010',
		'110011',
		'110100',
		'110101',
		'110110',
		'110111',
		'111000',
		'111001',
		'111010',
		'111011',
		'111100',
		'111101',
		'111110',
		'111111'
	];
	const BASE64_TABLE_CHAR = [
		'A',
		'B',
		'C',
		'D',
		'E',
		'F',
		'G',
		'H',
		'I',
		'J',
		'K',
		'L',
		'M',
		'N',
		'O',
		'P',
		'Q',
		'R',
		'S',
		'T',
		'U',
		'V',
		'W',
		'X',
		'Y',
		'Z',
		'a',
		'b',
		'c',
		'd',
		'e',
		'f',
		'g',
		'h',
		'i',
		'j',
		'k',
		'l',
		'm',
		'n',
		'o',
		'p',
		'q',
		'r',
		's',
		't',
		'u',
		'v',
		'w',
		'x',
		'y',
		'z',
		'0',
		'1',
		'2',
		'3',
		'4',
		'5',
		'6',
		'7',
		'8',
		'9',
		'+',
		'/'
	];

	let plain_text = '';
	let cipher_text = '';

	function str2bin(text: string) {
		let binary = '';
		for (let i = 0; i < text.length; i++) {
			let code = text.charCodeAt(i);
			binary += (code >>> 0).toString(2).padStart(8, '0');
		}
		return binary;
	}

	function bin2base64(binary: string) {
		let table_index = BASE64_TABLE_BINARY.findIndex((search) => search === binary);
		return BASE64_TABLE_CHAR[table_index];
	}

	function base64encode(plain_text: string) {
		let binary = str2bin(plain_text);
		let sequester_count = 0;
		let result = '';

		for (let i = 0; i < binary.length; i += BLOCK_SIZE) {
			let block = binary.substring(i, i + BLOCK_SIZE);

			if (block.length === 8) {
				sequester_count = 2;
				block += '0000000000000000';
			}

			if (block.length === 16) {
				sequester_count = 1;
				block += '00000000';
			}

			for (let j = 0; j < block.length; j += GROUP_SIZE) {
				let group = block.substring(j, j + GROUP_SIZE);
				result += bin2base64(group);
			}
		}

		if (sequester_count === 1) {
			return result.slice(0, -1).concat('=');
		}

		if (sequester_count === 2) {
			return result.slice(0, -2).concat('==');
		}

		return result;
	}

	// event type event: Event & { currentTarget: EventTarget & HTMLInputElement } why?
	function update() {
		let result = base64encode(plain_text);
		cipher_text = result;
	}
</script>

<h1>Base 64 Encoder</h1>
<div>
	<div>
		<input bind:value={plain_text} on:input={update} type="text" />
		<button>Delete</button>
	</div>

	<div>
		<input bind:value={cipher_text} type="text" disabled />
		<button>Copy</button>
	</div>
</div>

<style>
	input {
		width: 20rem;
	}
</style>
