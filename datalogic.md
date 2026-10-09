# Даталогическая модель базы данных (3НФ)

## 1. Справочник ролей (`Role`)
| Имя столбца | Тип данных | Ограничения | Описание |
| :--- | :--- | :--- | :--- |
| `role_id` | INT | PRIMARY KEY, AUTO_INCREMENT | Идентификатор роли |
| `role_name` | VARCHAR(50) | NOT NULL, UNIQUE | Название роли (ADMIN, PLAYER, DEVELOPER) |
| `role_description` | VARCHAR(250) | NULL | Описание полномочий |

## 2. Справочник стран (`Country`)
| Имя столбца | Тип данных | Ограничения | Описание |
| :--- | :--- | :--- | :--- |
| `cnt_id` | INT | PRIMARY KEY, AUTO_INCREMENT | Идентификатор страны |
| `cnt_name` | VARCHAR(100) | NOT NULL, UNIQUE | Название страны |
| `cnt_code_iso` | VARCHAR(3) | NOT NULL, UNIQUE | Трехбуквенный ISO код (BLR, RUS, etc.) |

## 3. Учётные записи пользователей (`User`)
| Имя столбца | Тип данных | Ограничения | Описание |
| :--- | :--- | :--- | :--- |
| `usr_id` | INT | PRIMARY KEY, AUTO_INCREMENT | Идентификатор пользователя |
| `usr_role_id` | INT | NOT NULL, FOREIGN KEY (`Role.role_id`) | Назначаемая роль |
| `usr_login` | VARCHAR(50) | NOT NULL, UNIQUE | Логин пользователя |
| `usr_email` | VARCHAR(100) | NOT NULL, UNIQUE | Электронная почта |
| `usr_password_hash` | VARCHAR(255) | NOT NULL | Хэш пароля |
| `usr_is_active` | BOOLEAN | NOT NULL, DEFAULT TRUE | Статус активности |
| `usr_created_at` | DATETIME | NOT NULL, DEFAULT CURRENT_TIMESTAMP | Дата регистрации |

## 4. Расширенный профиль пользователя (`UserProfile`)
| Имя столбца | Тип данных | Ограничения | Описание |
| :--- | :--- | :--- | :--- |
| `prf_usr_id` | INT | PRIMARY KEY, FOREIGN KEY (`User.usr_id`) | Ссылка 1:1 на пользователя |
| `prf_cnt_id` | INT | NULL, FOREIGN KEY (`Country.cnt_id`) | Страна проживания |
| `prf_display_name` | VARCHAR(100) | NOT NULL | Отображаемое имя |
| `prf_avatar_url` | VARCHAR(255) | NULL | Ссылка на аватар |
| `prf_birth_date` | DATE | NULL | Дата рождения |
| `prf_updated_at` | DATETIME | NOT NULL | Дата изменения профиля |

## 5. Журнал действий (`ActionLog`)
| Имя столбца | Тип данных | Ограничения | Описание |
| :--- | :--- | :--- | :--- |
| `log_id` | BIGINT | PRIMARY KEY, AUTO_INCREMENT | Идентификатор лога |
| `log_usr_id` | INT | NOT NULL, FOREIGN KEY (`User.usr_id`) | Пользователь |
| `log_action` | VARCHAR(100) | NOT NULL | Тип действия (LOGIN, PURCHASE, etc.) |
| `log_ip_address` | VARCHAR(45) | NOT NULL | IP-адрес клиента |
| `log_description` | VARCHAR(500) | NULL | Подробное описание события |
| `log_timestamp` | DATETIME | NOT NULL, DEFAULT CURRENT_TIMESTAMP | Время фиксации |

## 6. Студии-разработчики (`DeveloperStudio`)
| Имя столбца | Тип данных | Ограничения | Описание |
| :--- | :--- | :--- | :--- |
| `std_id` | INT | PRIMARY KEY, AUTO_INCREMENT | Идентификатор студии |
| `std_cnt_id` | INT | NULL, FOREIGN KEY (`Country.cnt_id`) | Страна регистрации студии |
| `std_name` | VARCHAR(100) | NOT NULL, UNIQUE | Название студии |
| `std_founded_at` | DATE | NULL | Дата основания |
| `std_website` | VARCHAR(255) | NULL | Веб-сайт |

## 7. Сотрудники студии (`StudioMember`)
| Имя столбца | Тип данных | Ограничения | Описание |
| :--- | :--- | :--- | :--- |
| `stm_usr_id` | INT | COMPOSITE PRIMARY KEY (1/2), FOREIGN KEY (`User.usr_id`) | Пользователь (разработчик) |
| `stm_std_id` | INT | COMPOSITE PRIMARY KEY (2/2), FOREIGN KEY (`DeveloperStudio.std_id`) | Студия |
| `stm_role` | VARCHAR(50) | NOT NULL | Должность (LEAD, DEVELOPER, QA) |
| `stm_joined_at` | DATETIME | NOT NULL | Дата присоединения |

## 8. Справочник жанров (`Genre`)
| Имя столбца | Тип данных | Ограничения | Описание |
| :--- | :--- | :--- | :--- |
| `gnr_id` | INT | PRIMARY KEY, AUTO_INCREMENT | Идентификатор жанра |
| `gnr_name` | VARCHAR(50) | NOT NULL, UNIQUE | Название жанра (RPG, Action, etc.) |

## 9. Каталог игр (`Game`)
| Имя столбца | Тип данных | Ограничения | Описание |
| :--- | :--- | :--- | :--- |
| `game_id` | INT | PRIMARY KEY, AUTO_INCREMENT | Идентификатор игры |
| `game_std_id` | INT | NOT NULL, FOREIGN KEY (`DeveloperStudio.std_id`) | Студия-издатель |
| `game_title` | VARCHAR(150) | NOT NULL | Название игры |
| `game_price` | DECIMAL(10, 2) | NOT NULL, CHECK (`game_price` >= 0) | Базовая стоимость |
| `game_release_date` | DATE | NOT NULL | Дата официального релиза |

## 10. Жанры игр (`GameGenre`)
| Имя столбца | Тип данных | Ограничения | Описание |
| :--- | :--- | :--- | :--- |
| `gg_game_id` | INT | COMPOSITE PRIMARY KEY (1/2), FOREIGN KEY (`Game.game_id`) | Идентификатор игры |
| `gg_gnr_id` | INT | COMPOSITE PRIMARY KEY (2/2), FOREIGN KEY (`Genre.gnr_id`) | Идентификатор жанра |

## 11. Дополнения к играм (`GameDLC`)
| Имя столбца | Тип данных | Ограничения | Описание |
| :--- | :--- | :--- | :--- |
| `dlc_id` | INT | PRIMARY KEY, AUTO_INCREMENT | Идентификатор дополнения |
| `dlc_game_id` | INT | NOT NULL, FOREIGN KEY (`Game.game_id`) | Базовая игра |
| `dlc_title` | VARCHAR(150) | NOT NULL | Название DLC |
| `dlc_price` | DECIMAL(10, 2) | NOT NULL, CHECK (`dlc_price` >= 0) | Цена DLC |
| `dlc_release_date` | DATE | NOT NULL | Дата выхода DLC |

## 12. Транзакции покупок (`Purchase`)
| Имя столбца | Тип данных | Ограничения | Описание |
| :--- | :--- | :--- | :--- |
| `pur_id` | BIGINT | PRIMARY KEY, AUTO_INCREMENT | Номер транзакции |
| `pur_usr_id` | INT | NOT NULL, FOREIGN KEY (`User.usr_id`) | Покупатель |
| `pur_game_id` | INT | NOT NULL, FOREIGN KEY (`Game.game_id`) | Купленная игра |
| `pur_amount` | DECIMAL(10, 2) | NOT NULL, CHECK (`pur_amount` >= 0) | Зафиксированная цена покупки |
| `pur_payment_method` | VARCHAR(50) | NOT NULL | Способ оплаты (CARD, PAYPAL, etc.) |
| `pur_status` | VARCHAR(20) | NOT NULL, DEFAULT 'COMPLETED' | Статус (COMPLETED, REFUNDED) |
| `pur_created_at` | DATETIME | NOT NULL, DEFAULT CURRENT_TIMESTAMP | Время проведения оплаты |

## 13. Отзывы пользователей (`Review`)
| Имя столбца | Тип данных | Ограничения | Описание |
| :--- | :--- | :--- | :--- |
| `rev_id` | INT | PRIMARY KEY, AUTO_INCREMENT | Идентификатор отзыва |
| `rev_usr_id` | INT | NOT NULL, FOREIGN KEY (`User.usr_id`) | Автор отзыва |
| `rev_game_id` | INT | NOT NULL, FOREIGN KEY (`Game.game_id`) | Оцениваемая игра |
| `rev_rating` | INT | NOT NULL, CHECK (`rev_rating` BETWEEN 1 AND 10) | Оценка от 1 до 10 |
| `rev_text` | TEXT | NULL | Текст отзыва |
| `rev_created_at` | DATETIME | NOT NULL, DEFAULT CURRENT_TIMESTAMP | Дата публикации |

## 14. Справочник достижений (`Achievement`)
| Имя столбца | Тип данных | Ограничения | Описание |
| :--- | :--- | :--- | :--- |
| `ach_id` | INT | PRIMARY KEY, AUTO_INCREMENT | Идентификатор достижения |
| `ach_game_id` | INT | NOT NULL, FOREIGN KEY (`Game.game_id`) | Привязанная игра |
| `ach_name` | VARCHAR(100) | NOT NULL | Название достижения |
| `ach_description` | VARCHAR(250) | NULL | Условия получения |
| `ach_points` | INT | NOT NULL, CHECK (`ach_points` > 0) | Очки ачивки |

## 15. Полученные достижения пользователей (`UserAchievement`)
| Имя столбца | Тип данных | Ограничения | Описание |
| :--- | :--- | :--- | :--- |
| `uach_usr_id` | INT | COMPOSITE PRIMARY KEY (1/2), FOREIGN KEY (`User.usr_id`) | Пользователь |
| `uach_ach_id` | INT | COMPOSITE PRIMARY KEY (2/2), FOREIGN KEY (`Achievement.ach_id`) | Достижение |
| `uach_unlock_date` | DATETIME | NOT NULL, DEFAULT CURRENT_TIMESTAMP | Время получения |

## 16. Игровые сессии / Аналитика (`PlaySession`)
| Имя столбца | Тип данных | Ограничения | Описание |
| :--- | :--- | :--- | :--- |
| `ps_id` | BIGINT | PRIMARY KEY, AUTO_INCREMENT | Идентификатор сессии |
| `ps_usr_id` | INT | NOT NULL, FOREIGN KEY (`User.usr_id`) | Игрок |
| `ps_game_id` | INT | NOT NULL, FOREIGN KEY (`Game.game_id`) | Запущенная игра |
| `ps_started_at` | DATETIME | NOT NULL | Время старта сессии |
| `ps_duration_sec` | INT | NOT NULL, CHECK (`ps_duration_sec` > 0) | Длительность в секундах |
| `ps_device` | VARCHAR(50) | NOT NULL | Платформа (PC, PS5, XBOX) |
