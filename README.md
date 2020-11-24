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
