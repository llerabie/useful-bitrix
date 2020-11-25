 ## Вывод HIGHLOAD блока, привязанному к разделу 
 ```php
if (Loader::includeModule('iblock') && Loader::includeModule('highloadblock')) {
	$db_list = CIBlockSection::GetList(
		array("ID" => "ASC"),
		array("IBLOCK_ID" => "1", "ID" => $arResult["VARIABLES"]["SECTION_ID"]),
		false,
		array("ID", "IBLOCK_ID", "UF_PROBLEMS")
	);
	if ($db_el = $db_list->GetNext()) {
		$hlblock = HighloadBlockTable::getById($arParams["PROBLEMS_HIGHLOAD_ID"])->fetch();
		$hlentity = HighloadBlockTable::compileEntity($hlblock);
		$hldataclass = $hlentity->getDataClass();

		$hl_db_list = $hldataclass::getList(array(
			'select' => array('*'),
			'filter' => array(
				'ID' => $db_el['UF_PROBLEMS']
			),
		));
		while ($hl_db_el = $hl_db_list->Fetch()) {
			$problems[] = $hl_db_el;
		}
	}
}
```
 ## Пример вывода элемента из инфоблока 
  ```php
CModule::IncludeModule('iblock');
$db_list = CIBlockElement::GetList(
	array("SORT" => "ASC"),
	array("IBLOCK_ID" => "2"),
	false,
	array(),
	array("ID", "IBLOCK_ID", "NAME", "PREVIEW_TEXT", "PROPERTY_*")
);
if ($db_el = $db_list->GetNextElement()) {
	$guide = $db_el->GetFields();
	$guide_props = $db_el->GetProperties();
}
```
 ## Пример вывода секции из инфоблока 
  ```php
// Marafon
$marafon_db_list = CIBlockSection::GetList(
	array(),
	array("ID" => $arParams["MARAFON_SECTION_ID"], "IBLOCK_ID" => $arParams["MARAFON_IBLOCK_ID"]),
	false,
	array("ID", "IBLOCK_ID", "UF_START", "UF_SALE_INFO")
);
if ($marafon_db_el = $marafon_db_list->GetNext()) {
	$arResult["MARAFON_CARD"] = $marafon_db_el;
}

```
 ## Сжатие картинки (фото)
 ```php
 foreach ($guide_props["GUIDE_LICENSE"]["VALUE"] as $photo) {
	$preview = CFile::ResizeImageGet(
		$photo,
		["width" => 400, "height" => 400],
		BX_RESIZE_IMAGE_PROPORTIONAL
	);
	$original = CFile::GetFileArray($photo);
	$photos[] = [
		"PREVIEW" => $preview["src"],
		"ORIGINAL" => $original["SRC"]
	];
}
 ```
  ## Ajax-запрос кнопки "Загрузить ещё"
  * Script.js
  ```php
  $(document).ready(function () {
	$(document).on(`click`, `a[data-url]`, () => {
		const targetContainer = $(document).find(`.reviews`);
		const url = $('a[data-url]').attr(`data-url`);

		if (url !== undefined) {
			$.ajax({
				type: `GET`,
				url: url,
				dataType: `html`,
				success: function (data) {
					$(`a[data-url]`).remove();

					const elements = $(data).find(`.reviews`).children();
					const pagination = $(data).find(`.col-xl-8.mx-auto.text-center.reveiws-btn`);
					targetContainer.append(elements.hide());
					targetContainer.children().fadeIn(700);
					targetContainer.parent().append(pagination);
				}
			});
		}
	});

});
 ```
 * Template.php 
  ```php
  <?
if (!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED !== true) die();

/** @var array $arResult */

$this->createFrame()->begin("Загрузка навигации");

if ($arResult["NavPageCount"] > 1):
	if ($arResult["NavPageNomer"] + 1 <= $arResult["nEndPage"]):
		$plus = $arResult["NavPageNomer"] + 1;
		$url = $arResult["sUrlPathParams"] . "PAGEN_" . $arResult["NavNum"] . "=" . $plus; ?>
		<div class="col-xl-8 mx-auto text-center reveiws-btn">
			<a href="javascript:void(0);" class="btn btn-primary" data-url="<?= $url ?>">
				<img src="<?= SITE_TEMPLATE_PATH ?>/assets/img/more.png" alt="more">
				Загрузить еще 4
			</a>
		</div>
	<?endif;
endif; ?>
 ```
